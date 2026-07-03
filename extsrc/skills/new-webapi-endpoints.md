---
name: new-webapi-endpoints
description: Add a Minimal API endpoint group.
---

1. Read `spec/extend/08-webapi-minimal.md`.
2. Add `Endpoints/{Feature}/{Feature}Endpoints.cs` with `Map{Feature}Endpoints(this IEndpointRouteBuilder)`.
3. `MapGroup("/api/v1/{area}")` + `.WithTags("{Feature}")`; group `.RequireAuthorization()` where needed.
4. Static handlers + method DI; API `record` types in same file.
5. Long work: enqueue via `IJobService`, return `202 Accepted`.
6. Auth/OpenAPI/SignalR in `{EntryPoint}/Extensions/`; map from `Program.cs`.
