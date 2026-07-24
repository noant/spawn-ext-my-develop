### Method arguments

- When a method, constructor, or record has **3 or fewer parameters**, keep them on one line (or break naturally if the line exceeds the column limit).
- When a method, constructor, or record has **more than 3 parameters**, place **every parameter on its own indented line**, including the first one.
- The opening parenthesis stays on the same line as the method/type name; the closing parenthesis goes on its own line at the same indent level as the declaration.
- This rule applies to: method declarations, method calls, constructor calls, primary constructors, record declarations, and attribute constructors.
- Exception: lambda parameters and inline delegate signatures may stay on one line regardless of count.

```csharp
// 3 or fewer — one line
public Task<Order> GetByIdAsync(string id, CancellationToken ct = default);
_ = new OrderDto("id", "name", 100m);

// More than 3 — each on its own line
public Task<Order> GetFilteredAsync(
    string status,
    DateTime from,
    DateTime to,
    int page,
    int pageSize,
    CancellationToken ct = default);

var result = await _service.GetFilteredAsync(
    status,
    from,
    to,
    page,
    pageSize,
    ct);

public sealed record PagedResult<T>(
    IReadOnlyCollection<T> Items,
    int TotalCount,
    int Page,
    int PageSize);

// Exception — lambda stays compact
items.Select((x, i) => (x, i));
```

### Fluent style

```csharp
items
    .Where(x => x.Active)
    .OrderBy(x => x.Name)
    .ToList();
```

- Prefer LINQ over loops when readability wins.
- Prefer local functions over private class methods when logic is used only inside one method and does not need reuse or isolated testing.
- When a method uses local functions, place a `// Local functions` comment after the main flow and before local declarations.

```csharp
public int Run(int n)
{
    var x = Step(n);
    // Local functions
    int Step(int v) => v + 1;
    return x;
}
```

### Dependency Injection
- Prefer DI over `new` for app services; inject via primary constructors.
- Prefer primary constructor syntax; avoid classic ctor + `private readonly` field boilerplate when a primary constructor is enough.
- Use `new` only for trivial types (DTOs, value objects) or outside the dependency graph.
- Application services: `sealed class` with primary constructor injection; public contracts are interfaces with `I` prefix.
- Multi-implementation: register variants and resolve via selector injected with `IEnumerable<T>`; prefer typed `IHandler<TCommand>` over reflection dispatch.
- Use `GetRequiredService<T>()` in hosts; avoid `IServiceProvider.GetService` in domain logic.

```csharp
// Prefer
public sealed class OrderService(IOrderStore store) : IOrderService
{
    public Task SaveAsync(OrderDto dto, CancellationToken cancellationToken = default)
        => store.SaveAsync(new Order(dto), cancellationToken);
}

// Avoid — service via new; classic field boilerplate
public class OrderService
{
    private readonly IOrderStore _store;
    public OrderService(IOrderStore store) => _store = store;
    public void Save(OrderDto dto) => new OrderStore().Save(new Order(dto));
}
```

### Naming
- PascalCase for types and public members; `_camelCase` for private fields; camelCase for parameters and locals.
- Async methods: `Async` suffix; last parameter `CancellationToken cancellationToken = default` (not `ct`).

```csharp
public Task LoadAsync(string id, CancellationToken cancellationToken = default);
```

### Tooling language
- Prefer C# for automation and tooling; use PowerShell/Bash only when requested or required for platform integration.

```text
dotnet run --project tools/MigrateDb
```

### Central Package Management (CPM)
- Always use CPM for C# projects (`Directory.Packages.props`, versionless `PackageReference`).
- Vulnerability audit: `dotnet list {Solution}.sln package --vulnerable --include-transitive` (high severity is the default gate).
- Fix direct packages by bumping the matching `PackageVersion` in CPM.
- Transitive high-severity packages: prefer bumping the top-level stack (e.g. EF Core, Npgsql EF provider) over pinning transitives, unless no patched upstream exists.
- When raising EF Core / ASP.NET Core patch level, align `Microsoft.Extensions.*`, `Microsoft.AspNetCore.*`, and `System.Text.Json` / `System.Net.Http.Json` to the same patch band to avoid NU1605 downgrades.
- After bumps (when verification is requested): restore, re-run the vulnerability list, then build.

```xml
<!-- Directory.Packages.props -->
<PackageVersion Include="Npgsql.EntityFrameworkCore.PostgreSQL" Version="8.0.11" />
<!-- csproj -->
<PackageReference Include="Npgsql.EntityFrameworkCore.PostgreSQL" />
```

### Error handling
- Use exceptions for failures; avoid bool/null/error codes as primary signals.
- No empty or overly broad `try`/`catch` — let exceptions propagate. Add `TryXxx`/`Result` only when asked.
- Do not swallow exceptions. Use `throw;` to preserve stack; when wrapping, set inner exception and add context (ids, endpoint, status code).
- One application/domain exception type for operational failures; wrap infrastructure exceptions with context.
- Non-critical paths (local cache, remote config fallbacks): catch, log, return null or default — do not fail the primary flow.
- Corrupted local resume metadata: log, delete the broken artifact, continue.

```csharp
// Prefer
catch (HttpRequestException ex)
{
    throw new AppException($"GET {url} failed", ex);
}

// Avoid — swallow / lose stack
catch (Exception) { return false; }
catch (Exception ex) { throw ex; }
```

### Immutability
- Prefer `record` for settings, parameters, and DTOs crossing assembly boundaries.
- Return `IReadOnlyCollection<T>` from public queries; use mutable classes only when in-place mutation or deserialization requires it.

```csharp
public sealed record UserDto(string Id, string Name);
public IReadOnlyCollection<UserDto> List();
```

### Async and cancellation
- Prefer async I/O; last parameter: `CancellationToken cancellationToken = default`.
- Propagate `cancellationToken` through downstream async calls.
- Return `Task.CompletedTask` / `Task.FromResult` for sync-completed async paths.
- Exclude `OperationCanceledException` from retry policies.
- Parallel branches that need scoped services: `CreateAsyncScope()` inside each branch; do not share scoped instances across concurrent tasks.
- Worker/scheduler cancel and checkpoint rules: see `09-processors-workers-jobs.md`.

```csharp
// Prefer
public async Task SaveAsync(Order order, CancellationToken cancellationToken = default)
    => await _store.SaveAsync(order, cancellationToken);

// Avoid — drop token / share scoped service across parallel work
await _store.SaveAsync(order);
await Parallel.ForEachAsync(ids, async (id, _) => await _scoped.HandleAsync(id));
```