---
name: new-implementation-project
description: Scaffold a technology-specific implementation.
---

1. Read `spec/extend/10-implementation-projects.md`, `spec/extend/06-abstractions-layer.md`, `spec/extend/07-di-registration.md`.
2. Name `{Product}.{Domain}.{Capability}.{TechnologyOrVariant}`.
3. Implement abstraction from `{Domain}.Abstractions`; `{MainService}.cs` + `Configuration/{Feature}Options.cs`.
4. `Extensions/{Feature}ServiceCollectionExtensions.cs` with `Add{Technology}{Capability}()`.
5. Refs: abstractions + shared infra + tech packages only.
6. Register in entry point `Program.cs`; keyed when multiple instances coexist.
