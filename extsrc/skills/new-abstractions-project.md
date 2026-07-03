---
name: new-abstractions-project
description: Scaffold a feature Abstractions project.
---

1. Read `spec/extend/02-csharp-solution-layout.md`, `spec/extend/06-abstractions-layer.md`.
2. Create `{Product}.{Feature}.Abstractions` (nullable, implicit usings, shared TFM).
3. Interfaces at root; `Models/` / `Events/` when many types.
4. `I{Role}` naming; `sealed record` DTOs; async + `CancellationToken cancellationToken = default`.
5. Abstractions refs only; register concretes in impl `Extensions/`.
