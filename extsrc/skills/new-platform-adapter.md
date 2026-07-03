---
name: new-platform-adapter
description: Scaffold a desktop platform adapter project.
---

1. Read `spec/extend/02-csharp-solution-layout.md`, `spec/extend/06-abstractions-layer.md`, `spec/extend/16-desktop-launcher-packaging.md`.
2. Create `{Product}.{Platform}` classlib; refs: `{Product}.Shared` + vendor SDK only.
3. Implement `I{Domain}Provider` (or swap interface from Shared); public sealed provider, internal SDK helpers.
4. Optional `[{Platform}ProviderName("key")]` on provider class.
5. Add `{Platform}ServiceCollectionExtensions.Add{Platform}(IServiceCollection)` when more than a single registration.
6. Register in host: `AddSingleton<I{Domain}Provider, {Platform}Provider>()` or keyed/discovery wiring.
