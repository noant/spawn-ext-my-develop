# C# / .NET — dependency injection and registration

### Registration surface
- One static `{Feature}ServiceCollectionExtensions` in `Extensions/` per impl project.
- Methods: `Add{Provider|Persistence|Worker}(...)`; return `IServiceCollection` for chaining.

### Signatures and options
- `(IServiceCollection, IConfiguration root, string sectionName = "...")` for libraries with a default section name.
- Pre-resolved `(IServiceCollection, IConfiguration section)` when the entry point chooses the config path (e.g. per-job worker sections).
- Libraries supply defaults; entry points own config hierarchy. Optional `serviceKey = "default"`; optional worker flags.
- Options in `Configuration/`; bind with `ValidateDataAnnotations().ValidateOnStart()` or shared `AddValidatedOptions<T>`.
- Keyed options: named `{sectionName}:{serviceKey}`; nested composite keys: `{RootSection}:{instanceKey}:{SubFeature}`.

### Keyed services
- Use when multiple impls of one interface share a host.
- Register key catalog via `AddKeyedServiceKey<TInterface>`; prefer `AddKeyedScopedWithOptions<...>` helpers.
- Known key: `GetRequiredKeyedService<T>(key)`; discovery: inject keyed-services helper.

### Lifetimes
- Scoped: domain/session, DbContext stores, keyed per-operation providers.
- Singleton: stateless infra, caches, keyed calculators.
- Transient: lightweight per-call objects.
- HostedService: polling, migrations, watchers.
- DbContextFactory: singleton; short-lived contexts per operation.

### Composition
- Entry points chain `Add*` in `Program.cs`; impl projects never assume a host.
- Composite `Add*` wires a vertical slice behind one config key and optional flags (`registerWorker`, `registerWatcher`).
- Shared singleton registrations inside composite bundles must be idempotent (guard or register once from entry point).
- Config pack project (`{Product}.Shared.Configuration`) merges layered JSON + env vars; entry points call `Add{Product}Configurations(profiles...)`.
- Auth, SignalR, Swagger, host observers — register in `{EntryPoint}/Extensions/`, not domain libraries.
