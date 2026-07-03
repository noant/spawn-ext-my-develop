# Tools — MCP and pluggable modules

### MCP
- Use Context7 MCP for library/API docs, setup, and config steps without being asked.

### Pluggable tool modules
- `{Product}.{Feature}Tools.{ToolName}` — one module per swappable tool (router, loaders, workspace helpers).
- Each tool project owns `Extensions/{Tool}ServiceCollectionExtensions.cs` with `Add{Tool}(...)`.
- Router/cache invalidation lives in a dedicated tool module; entry points register tools via chained `Add*`.
- Tools depend on abstractions only; no entry-point or persistence references.
