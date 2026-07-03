# C# / .NET — processors, workers, and jobs

### Roles
- Processor: use-case orchestration; DI class, no hosting.
- Worker: poll loop (`BackgroundService` or shared base); one iteration per cycle.
- EntryPoint: composition root; may call processor directly for interactive flows.

### Processor
- Depends on abstractions; DI or scoped/keyed resolution.
- Batch sync: `I{Feature}Processor.RunAsync(CancellationToken)`; worker delegates each iteration.
- Interactive: transient processor; entry point calls step methods.

### Worker
- Shared polling base: iteration first, then delay; log start/finish; log errors, keep host alive.
- Worker file stays thin (~30 lines): inject processor, call `RunAsync` in `IterationAsync`.
- `CreateAsyncScope()` per iteration/batch when scoped services needed; never inject scoped services into worker ctor.
- Keyed resolution for multi-instance domains.

### Jobs
- Split: `{Domain}.Jobs.Abstractions`, `.Persistence.{Provider}`, `.{Runtime}`.
- Handler modules: `{Domain}Workers.{HandlerName}` — `{WorkItem}Job` + `IJobHandler<{WorkItem}Job>`; type name = payload type name.
- One `AddJobWorker<TPayload>(section)` per payload type; named options `JobWorker_{PayloadTypeName}` for parallel hosted workers.
- Status machine: ready -> running (optimistic mark before execute) -> complete/error/cancelled; keyed schedule calculators; retries at worker.
- Queue ordering: group by queue name, order by inner order; optional max parallel queues.
- Startup: reset orphaned `Running` to `Ready`; per-job cancellation tokens; distinguish user cancel from host shutdown.

### Registration
- Each layer: `Add{Feature}(IConfiguration, ...)`; entry points chain.
- Composite modules bundle source/index/processor/worker behind flags + one config key.
