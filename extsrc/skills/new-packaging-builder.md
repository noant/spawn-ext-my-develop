---
name: new-packaging-builder
description: Scaffold a packaging Builder CLI for desktop distributions.
---

1. Read `spec/extend/16-desktop-launcher-packaging.md`, `spec/extend/02-csharp-solution-layout.md`.
2. Create `{Product}.Builder` Exe (`net8.0`); no project references to host or Shared.
3. `Program.cs` with `System.CommandLine`: options for client ids, API URLs, platform id, launch template, dist dir.
4. Handler: timestamped dist root, copy game client tree, copy published host from `{product-short}/` beside builder exe.
5. Read host config JSON, replace `{{ token }}` placeholders, write final config; print dist path to stdout.
6. Document two-step publish in `build.{script}`; gitignore bundle output folder.
