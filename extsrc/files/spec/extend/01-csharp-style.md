# C# / .NET — style and practices

### Method arguments
- 1-3 arguments: keep on one line.
- More than 3: each argument on its own indented line (including the first one).
- Applies to declarations and calls.
- Lambda parameters: stay on one line regardless of count.

```csharp
// 1-3 — one line
Foo(a, b, c);
public Task<Order> GetByIdAsync(string id, CancellationToken ct = default);

// More than 3 — each on own line
Foo(
    a,
    b,
    c,
    d);
public Task<Order> GetFilteredAsync(
    string status,
    DateTime from,
    DateTime to,
    int page,
    int pageSize,
    CancellationToken ct = default);

// Lambda — stays compact
items.Select((x, i) => (x, i));
```

### Fluent style
- Prefer declarative/fluent code when clearer: LINQ, EF fluent API, builders, method chaining, options/config setup.
- Long call chains: keep the receiver and first segment on the first line; put each following `.` member/call on its own indented line (leading dot). Short single-segment calls stay on one line.

```csharp
// LINQ
items
    .Where(x => x.Active)
    .OrderBy(x => x.Name)
    .ToList();

// EF Core query
dbContext.Orders
    .Include(o => o.Items)
    .Where(o => o.Status == OrderStatus.Active)
    .ToListAsync(ct);

// Builder
new OrderBuilder()
    .WithCustomer(customerId)
    .WithItems(items)
    .Build();

// String / options configuration
services
    .AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options => { /* ... */ });
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
```

### Naming
- PascalCase for types and public members; `_camelCase` for private fields; camelCase for parameters and locals.
- Async methods: `Async` suffix; last parameter `CancellationToken cancellationToken = default` (not `ct`).

```csharp
public Task LoadAsync(string id, CancellationToken cancellationToken = default);
```

### Central Package Management (CPM)
- Always use CPM: `Directory.Packages.props` + versionless `PackageReference`.
- Audit: `dotnet list {Solution}.sln package --vulnerable --include-transitive` (high severity is the gate).
- Direct vulnerable packages: bump the matching `PackageVersion`.
- Transitive high-severity: bump the top-level stack (e.g. EF Core) over pinning transitives, unless no patched upstream exists.
- EF Core / ASP.NET Core patch bumps: align `Microsoft.Extensions.*`, `Microsoft.AspNetCore.*`, `System.Text.Json` / `System.Net.Http.Json` to the same patch band (NU1605).
- After bumps (when verification is requested): restore, re-audit, build.

```xml
<!-- Directory.Packages.props -->
<PackageVersion Include="Npgsql.EntityFrameworkCore.PostgreSQL" Version="8.0.11" />
<!-- csproj -->
<PackageReference Include="Npgsql.EntityFrameworkCore.PostgreSQL" />
```

### Error handling
- Exceptions, not bool/null codes.
- No empty/broad `catch`; `TryXxx`/`Result` only when asked.
- `throw;` not `throw ex`; wrap with inner + context.
- One domain exception type.

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
- `record` for settings, params, cross-assembly DTOs.
- `IReadOnlyCollection<T>` from public queries; mutable classes only when mutation/deserialization requires it.

```csharp
public sealed record UserDto(string Id, string Name);
public IReadOnlyCollection<UserDto> List();
```

### Async and cancellation
- Async I/O; last parameter `CancellationToken cancellationToken = default`; propagate downstream.
- `Task.CompletedTask` / `Task.FromResult` for sync-completed paths.
- Exclude `OperationCanceledException` from retries.
- Parallel branches with scoped services: `CreateAsyncScope()` per branch; never share scoped instances across tasks.
- Cancel/checkpoint rules: see `09-processors-workers-jobs.md`.

```csharp
// Prefer
public async Task SaveAsync(Order order, CancellationToken cancellationToken = default)
    => await _store.SaveAsync(order, cancellationToken);

// Avoid — drop token / share scoped service across parallel work
await _store.SaveAsync(order);
await Parallel.ForEachAsync(ids, async (id, _) => await _scoped.HandleAsync(id));
```