# C# / .NET — solution layout and registration

### Project structure
- Flat layout: one folder = one project at solution root; solution name `{Product}.sln`.
- Frontend/npm apps may live beside the solution but are not .NET projects in the solution file.
- `{Product}.Abstractions` — shared domain types; `{Product}.{Feature}.Abstractions` — feature contracts.
- `{Product}.{Feature}.{TechnologyOrVariant}` — concrete providers (e.g. `LovelyApp.Search.PostgreSQL`).
- `{Product}.{Feature}.Processor[.{Version}]` or `{Product}.{Feature}.Simple.{Version}` — use-case orchestration, no hosting.
- `{Product}.{Feature}.Worker` — optional polling wrapper around a processor.
- `{Product}.{Feature}Workers.{HandlerName}` — job handler module (payload + `IJobHandler<T>`); not a poll worker.
- `{Product}.{Feature}Tools.{ToolName}` — pluggable tool modules (router, loaders, helpers).
- `{Product}.{Domain}.EntryPoint.{WebApi|Console}.{Version}` — runnable host for one domain.
- `{Product}.Worker.EntryPoint.{WebApi|Console}.{Version}` — cross-domain worker host (agent, jobs, workspace stack).
- `{Product}.{Domain}.Jobs.Abstractions` / `.Persistence.{Provider}` / `.{Runtime}` — job contracts, storage, engine.
- `{Product}.Shared.{Configuration|Infrastructure|Infrastructure.Ef}` — config merge, DI helpers, EF factory.

### Registrar (`*.Registrar`) — optional, rarely used
- May aggregate `Add*` chains for reuse; most solutions compose DI directly in entry-point `Program.cs`.
- Each impl project still owns its `{Feature}ServiceCollectionExtensions`.

### Registration signature
- `Add{Capability}(this IServiceCollection services, IConfiguration configuration, string configurationSectionName = default, string serviceKey = default)` — default key is `"default"`.
- Pre-resolved subtree: `Add{Capability}(this IServiceCollection services, IConfiguration configSection)`.
