---
name: new-service-registration
description: Add ServiceCollectionExtensions with options and keyed DI.
---

1. Read `spec/extend/07-di-registration.md`, `spec/extend/10-implementation-projects.md`.
2. Add `Extensions/{Feature}ServiceCollectionExtensions.cs` with `Add{Capability}(this IServiceCollection services, IConfiguration configurationSection, string serviceKey = "default")` — no separate section name parameter.
3. Bind options: `ValidateDataAnnotations().ValidateOnStart()`; keyed: `{sectionPath}:{serviceKey}`.
4. Lifetime: Scoped (domain), Singleton (infra), HostedService (loops).
5. Keyed: `AddKeyedServiceKey<TInterface>(serviceKey)` when needed.
6. Chain `Add*` in entry point `Program.cs`.
