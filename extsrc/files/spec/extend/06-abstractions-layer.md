# C# / .NET — abstractions layer

### Role
- One `{Product}.{Feature}.Abstractions` library per feature; shared types in `{Product}.Abstractions`.
- Contracts only — implementations live in separate projects.

### Layout
- Service interfaces at root namespace; DTOs in `.Models`, events in `.Events` when needed.
- Large shared libraries: folders match namespaces (`Chat/` -> `{Product}.Abstractions.Chat`).
- One type per file; co-locate DTO with interface only if unused elsewhere.

### Naming
- Interfaces: `I{Thing}Service|Manager|Factory|Repository|Provider|Handler<T>|Observer`.
- Immutable snapshots: `sealed record`; computed/mutable refs: `sealed class` with `required`/`init`.
- Options: `*Options`; events: `*Event`; exceptions: `*Exception` (sealed, with ids).

### Contracts
- I/O: async + `CancellationToken` last (default); sync only for in-memory ops.
- Prefer `IReadOnlyList<T>` / `IReadOnlyDictionary<,>`; `T?` for not-found.
- Domain exceptions in abstractions when callers must catch/translate.

### Dependencies
- Reference abstractions only — no impl, persistence, or hosting.
- Register concretes in `{Implementation}/Extensions/` (optional no-op `Add{Feature}Abstractions()` stub only).
- Public contract constants/enums yes; secrets, connection strings, framework config types no.
