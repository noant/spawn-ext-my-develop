# C# / .NET — style and practices

### Fluent code style preference
- Prefer declarative/fluent style when it reads clearer: LINQ chains, EF Core fluent API, HttpClient-style configuration, builders, and method chaining.
- Use LINQ (`Where`, `Select`, `OrderBy`, …) instead of loops when readability wins; use builders for complex object setup when they help.

### Dependency Injection preference
- Prefer DI over `new` for app services: register in the container, inject via constructors (not inside the class body).
- Use `new` only for trivial types (DTOs, value objects) or objects outside the dependency graph.

### Tooling and automation language
- Prefer using C# .NET code for all tasks, automation, and tooling instead of PowerShell, Bash, or other scripting languages.
- Use C# console applications, scripts, or tools when automation or file operations are needed.
- Only use PowerShell, Bash, or other scripting languages when the user explicitly requests them or when they are required for specific platform integration.

### Central Package Management (CPM)
- When working with C#, always use Central Package Management (CPM).

### Error handling
- Use exceptions for failure paths; avoid `bool`, `null`, or ad-hoc error codes as the primary signal.
- Do not add empty or overly broad `try`/`catch` blocks—let exceptions propagate from lower layers. Add `TryXxx` methods or `Result`-style APIs only if the user asks for non-throwing surfaces.
- Do not swallow exceptions. Prefer `throw;` to rethrow without losing the stack when you only need side effects (e.g. logging) in `catch`. When wrapping, pass the caught exception into the new exception’s constructor (inner exception) and add useful context (ids, endpoint, HTTP status code, etc.).
