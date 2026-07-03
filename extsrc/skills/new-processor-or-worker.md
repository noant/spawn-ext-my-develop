---
name: new-processor-or-worker
description: Add Processor, Worker, or Job handler.
---

1. Read `spec/extend/09-processors-workers-jobs.md`, `spec/extend/07-di-registration.md`.
2. **Processor:** `{Feature}.Processor.{Version}` — `I{Feature}Processor.RunAsync` or step methods; abstractions only.
3. **Worker:** `{Feature}.Worker` — polling base, `IterationAsync` -> processor; `AddHostedService`.
4. **Job handler:** `{Feature}Workers.{HandlerName}` — `{WorkItem}Job` + `IJobHandler<{WorkItem}Job>`; scoped handler + `AddJobWorker<TPayload>(section)`.
5. Scopes per iteration for scoped/keyed services; keyed resolution for multi-instance domains.
6. Expose `Add{Feature}(IConfiguration, ...)`; chain in entry point.
