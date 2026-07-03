# C# / .NET — implementation projects

### Naming
- `{Product}.{Domain}.{Capability}.{TechnologyOrVariant}` — tech suffix = swappable backend (e.g. `PostgreSQL`, `SimpleV1`).
- Implement `{Domain}.Abstractions` only; no new public swap interfaces (deploy exes excepted).
- One primary abstraction per project; multiple interfaces only if tightly coupled or keyed variants.

### Layout
```
{Project}/
├── Extensions/{Feature}ServiceCollectionExtensions.cs
├── Configuration/{Feature}Options.cs
├── {MainService}.cs
├── Entities/ | Migrations/   # EF only
└── Workers/                  # optional
```

### Registration
- One primary `Add{Technology}{Capability}(IServiceCollection, IConfiguration, string sectionName, string serviceKey = "default")`.
- Validate options on start; Scoped or keyed when multiple instances; early-return if optional config missing.
- Entry points chain `Add*`; no entry-point or unrelated impl references (except composition bundles).

### Dependencies and visibility
- Allowed: abstractions, shared infra, tech NuGet. Not allowed: entry points, host packages in pure libs, unrelated impls.
- Providers: `internal` or `sealed public`; helpers/factories/workers: `internal`.

### Cardinality
- Adapter: one interface. Variants: N classes, different keys. Subsystem: 2–4 related interfaces. Reference stack: many interfaces. Bundle: facade delegating internally.
