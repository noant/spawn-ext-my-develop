---
name: new-service-registration
description: Add ServiceCollectionExtensions with options and keyed DI.
---

1. Read `spec/extend/07-di-registration.md`, `spec/extend/10-implementation-projects.md`.
2. Add `Extensions/{Feature}ServiceCollectionExtensions.cs` with `Add{Capability}(IServiceCollection, IConfiguration, string sectionName, string serviceKey = "default")`.
3. Bind options: `ValidateDataAnnotations().ValidateOnStart()`; keyed: `{section}:{serviceKey}`.
4. Lifetime: Scoped (domain), Singleton (infra), HostedService (loops).
5. Keyed: `AddKeyedServiceKey<TInterface>(serviceKey)` when needed.
6. Chain `Add*` in entry point `Program.cs`.
