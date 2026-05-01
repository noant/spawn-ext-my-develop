# C# / .NET — solution layout and registration

### Project structure
- Each folder/subfolder is a project unit; the solution root can use a product-style name (e.g. `LovelyApp.*`).
- Subprojects are folders with their own name and optional entry point (e.g. `LovelyApp.Walle.*` for Linux mini-robot software).
- `*.EntryPoint.*` hosts the runnable app (Web API, console, desktop)—e.g. `LovelyApp.Client.EntryPoint.Console`.
- `*.Processor` wires abstractions and runs the use-case flow (e.g. `LovelyApp.Client.Processor`).
- Use extra name segments to spell out the role/tech (e.g. `LovelyApp.Search.PostgreSQL` = PostgreSQL-backed search).
- `*.Abstractions` holds interfaces/DTO contracts; concrete providers live in named impl projects (e.g. interfaces in `LovelyApp.Search.Abstractions`, implementation in `LovelyApp.Search.PostgreSQL`).

### Registrar projects (`*.Registrar`)
- For projects like `*.EntryPoint`, the `*.Registrar` project can be reused. It contains a static `ServiceCollectionExtensions` class with an `Add*[SubprojectName]Services()` method for the EntryPoints. It accumulates all registrations of implementations.

### Service registration signature

- For services registrations use pattern like this: `IServiceCollection ServiceCollectionExtensions.AddSomeService(this IServiceCollection services, IConfiguration configuration, string configurationSectionName = default, string serviceKey = default);`. If `serviceKey` is default then use the `"default"` string.
