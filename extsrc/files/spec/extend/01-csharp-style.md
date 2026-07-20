# C# / .NET — style and practices

### Fluent style
- Prefer declarative/fluent code when clearer: LINQ, EF fluent API, builders, method chaining.
- Prefer LINQ over loops when readability wins.
- Prefer local functions over private class methods when logic is used only inside one method and does not need reuse or isolated testing.
- When a method uses local functions, place a `// Local functions` comment after the main flow and before local declarations.

### Dependency Injection
- Prefer DI over `new` for app services; inject via primary constructors.
- Prefer primary constructor syntax; avoid classic ctor + `private readonly` field boilerplate when a primary constructor is enough.
- Use `new` only for trivial types (DTOs, value objects) or outside the dependency graph.
- Application services: `sealed class` with primary constructor injection; public contracts are interfaces with `I` prefix.
- Multi-implementation: register variants and resolve via selector injected with `IEnumerable<T>`; prefer typed `IHandler<TCommand>` over reflection dispatch.
- Use `GetRequiredService<T>()` in hosts; avoid `IServiceProvider.GetService` in domain logic.

### Naming
- PascalCase for types and public members; `_camelCase` for private fields; camelCase for parameters and locals.
- Async methods: `Async` suffix; last parameter `CancellationToken cancellationToken = default` (not `ct`).

### Tooling language
- Prefer C# for automation and tooling; use PowerShell/Bash only when requested or required for platform integration.

### Central Package Management (CPM)
- Always use CPM for C# projects (`Directory.Packages.props`, versionless `PackageReference`).
- Vulnerability audit: `dotnet list {Solution}.sln package --vulnerable --include-transitive` (high severity is the default gate).
- Fix direct packages by bumping the matching `PackageVersion` in CPM.
- Transitive high-severity packages: prefer bumping the top-level stack (e.g. EF Core, Npgsql EF provider) over pinning transitives, unless no patched upstream exists.
- When raising EF Core / ASP.NET Core patch level, align `Microsoft.Extensions.*`, `Microsoft.AspNetCore.*`, and `System.Text.Json` / `System.Net.Http.Json` to the same patch band to avoid NU1605 downgrades.
- After bumps (when verification is requested): restore, re-run the vulnerability list, then build.

### Error handling
- Use exceptions for failures; avoid bool/null/error codes as primary signals.
- No empty or overly broad `try`/`catch` — let exceptions propagate. Add `TryXxx`/`Result` only when asked.
- Do not swallow exceptions. Use `throw;` to preserve stack; when wrapping, set inner exception and add context (ids, endpoint, status code).
- One application/domain exception type for operational failures; wrap infrastructure exceptions with context.
- Non-critical paths (local cache, remote config fallbacks): catch, log, return null or default — do not fail the primary flow.
- Corrupted local resume metadata: log, delete the broken artifact, continue.

### Immutability
- Prefer `record` for settings, parameters, and DTOs crossing assembly boundaries.
- Return `IReadOnlyCollection<T>` from public queries; use mutable classes only when in-place mutation or deserialization requires it.

### Async and cancellation
- Prefer async I/O; last parameter: `CancellationToken cancellationToken = default`.
- Propagate `cancellationToken` through downstream async calls.
- Return `Task.CompletedTask` / `Task.FromResult` for sync-completed async paths.
- Exclude `OperationCanceledException` from retry policies.
- Parallel branches that need scoped services: `CreateAsyncScope()` inside each branch; do not share scoped instances across concurrent tasks.
- Worker/scheduler cancel and checkpoint rules: see `09-processors-workers-jobs.md`.
