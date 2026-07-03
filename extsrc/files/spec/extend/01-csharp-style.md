# C# / .NET — style and practices

### Fluent style
- Prefer declarative/fluent code when clearer: LINQ, EF fluent API, builders, method chaining.
- Prefer LINQ over loops when readability wins.
- Prefer local functions over private class methods when logic is used only inside one method and does not need reuse or testing in isolation.

### Dependency Injection
- Prefer DI over `new` for app services; inject via constructors; store deps in `private readonly` fields.
- Use `new` only for trivial types (DTOs, value objects) or outside the dependency graph.
- Application services: `sealed class` with constructor injection; public contracts are interfaces with `I` prefix.
- Multi-implementation: register variants and resolve via selector injected with `IEnumerable<T>`; prefer typed `IHandler<TCommand>` over reflection dispatch.
- Use `GetRequiredService<T>()` in hosts; avoid `IServiceProvider.GetService` in domain logic.

### Naming
- PascalCase for types and public members; `_camelCase` for private fields; camelCase for parameters and locals.
- Async methods: `Async` suffix; last parameter `CancellationToken cancellationToken = default` (not `ct`).

### Tooling language
- Prefer C# for automation and tooling; use PowerShell/Bash only when requested or required for platform integration.

### Central Package Management (CPM)
- Always use CPM for C# projects.

### Error handling
- Use exceptions for failures; avoid bool/null/error codes as primary signals.
- No empty or overly broad `try`/`catch` — let exceptions propagate. Add `TryXxx`/`Result` only when asked.
- Do not swallow exceptions. Use `throw;` to preserve stack; when wrapping, set inner exception and add context (ids, endpoint, status code).
- One application/domain exception type for operational failures; wrap infrastructure exceptions with context.

### Immutability
- Prefer `record` for settings, parameters, and DTOs crossing assembly boundaries.
- Return `IReadOnlyCollection<T>` from public queries; use mutable classes only when in-place mutation or deserialization requires it.

### Async and cancellation
- Prefer async I/O; last parameter: `CancellationToken cancellationToken = default`.
- Use `cancellationToken` in public contracts (not `ct`); propagate through downstream async calls.
- Return `Task.CompletedTask` / `Task.FromResult` for sync async paths.
- Workers log iteration failures and continue; distinguish host shutdown from unexpected errors.
- Exclude `OperationCanceledException` from retry policies.
- In scheduler/worker loops, catch `TaskCanceledException` when `ex.CancellationToken == cancellationToken` — treat as pause/shutdown; other cancellations remain faults.
- After a successful unit of work, persist checkpoints (job markers, download progress) with `CancellationToken.None` so user cancel does not skip recording completed work.
- Parallel branches that need scoped services: `CreateAsyncScope()` inside each branch; do not share scoped instances across concurrent tasks.

### Local functions
- When a method uses local functions, place a `// Local functions` comment after the main flow and before local declarations.

### Optional / cache I/O
- Non-critical paths (local cache, remote customization/config fallbacks): catch, log, return null or default — do not fail the primary flow.
- Corrupted local resume metadata: log, delete the broken artifact, continue.

### HTTP client error branching
- For APIs with structured error payloads, compare known semantic error codes for expected branches; reserve exceptions for unexpected HTTP failures (small local `EnsureSuccessStatusCode` helper is fine).
