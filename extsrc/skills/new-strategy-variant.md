---
name: new-strategy-variant
description: Scaffold a pluggable strategy variant with jobs and executors.
---

1. Read `spec/extend/02-csharp-solution-layout.md`, `spec/extend/10-implementation-projects.md`, `spec/extend/15-cli-client-strategies.md`.
2. Create `{Product}.Strategies.{Operation}.{Variant}` (nullable, implicit usings, shared TFM).
3. Add `{Variant}{Operation}Strategy.cs`, `{Variant}{Operation}JobsGenerator.cs` implementing abstractions from `{Product}.Base` or feature abstractions.
4. Folders: `Jobs/` (concrete `I{Feature}Job` types), `JobExecutors/` (`I{Feature}JobExecutor<TJob>`).
5. `ServiceCollectionExtensions.cs`: `Add{Variant}{Operation}(IServiceCollection[, {Tool}Settings])` — register generator + executors.
6. Refs: abstractions + `{Product}.Strategies.{Operation}.Base` + helpers only; no host or client references unless aggregator requires.
