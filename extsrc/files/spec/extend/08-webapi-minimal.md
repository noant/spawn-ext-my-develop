# C# / .NET — Web API (Minimal APIs)

### Entry point
- `{Product}.{Domain}.EntryPoint.WebApi.{Version}` — HTTP/SignalR only; logic in abstractions/processors.
- Named config profile merged with shared domain settings.

### Routing
- REST prefix `/api/{version}/` (match project version suffix).
- One `{Feature}Endpoints` class; `Map{Feature}Endpoints(this IEndpointRouteBuilder)` with `.WithTags("{Feature}")`.
- Static handlers + method DI; no service instantiation in handlers.

### Auth
- `.RequireAuthorization()` on protected groups; `.AllowAnonymous()` on public routes only.
- Auth/CORS in `{EntryPoint}/Extensions/`; JWT bearer + refresh-token cookie flow in `{EntryPoint}/Auth/`.
- Refresh cookie path scoped to auth routes (e.g. `/api/{version}/auth`); rotate/revoke on logout.
- SignalR: query-string `access_token` for WebSocket upgrade via JWT bearer events.

### DTOs and delegation
- API-specific `record` types in endpoint file; bind domain models for thin CRUD.
- No AutoMapper unless adopted solution-wide.
- Long work: enqueue via `IJobService`, return `202 Accepted` — do not await processor in handler.

### Cross-cutting
- Hubs at `/hubs/{name}`; same auth as REST; hub methods that mirror REST enqueue the same job payload type.
- Domain push via host observers (`I{Domain}Observer` → SignalR `IHubContext`); handlers stay auth-agnostic.
- Entry-point-local outbox jobs: handler + `AddJobWorker<TPayload>` bundled in `{EntryPoint}/Extensions/`.
- OpenAPI/SignalR/middleware in `{EntryPoint}/Extensions/`; `Program.cs` — orchestration + `Map*` only.
- Swagger: global Bearer JWT + `AddEndpointsApiExplorer()`.

### HTTP semantics
- Explicit status codes: `404`, `401`, `201`/`204`/`202`. Per-handler `Results.*` OK; global `ProblemDetails` only if consistent.
