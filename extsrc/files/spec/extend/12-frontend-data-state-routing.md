# Frontend — data, state, routing, and real-time

### HTTP client
- One shared HTTP client from `shared/api/` with configurable base URL.
- Inject auth via accessors (`getAccessToken`, `setAccessToken`, `onLogout`) wired once at the auth boundary — do not import feature stores from the HTTP module.
- On 401, attempt a single token refresh (dedupe in-flight refresh), retry the original request once, then run centralized session teardown on failure.
- Refresh/login calls that rely on cookies use `withCredentials: true` on a dedicated refresh URL.

### Feature API modules
- Each feature owns `features/{Feature}/api.ts`: async functions only, no React.
- Map wire DTOs ↔ UI models in the API module; UI and hooks consume UI shapes only.
- Export `{feature}QueryKeys` from the same file; keys are hierarchical tuples (`["domain", "list", params]`, `["domain", "item", id]`).

### Server state (React Query)
- Wrap the app in a `QueryClientProvider` with conservative defaults (e.g. `refetchOnWindowFocus: false`, limited retry).
- Use `useQuery` / `useMutation`; call feature API functions in `queryFn` / `mutationFn`.
- After mutations, `invalidateQueries` for affected keys; use `setQueryData` for incremental updates.
- Use `enabled` for conditional fetches; reuse cached list data via `getQueryData` when a derived client-side query is cheaper.

### Client state (Zustand)
- Use Zustand for UI/session state that outlives a single component: tabs, selection, panel visibility, modal/palette open, in-flight agent status.
- Do not mirror server collections in Zustand; treat React Query as the source of truth for remote data.
- Persist only small prefs (auth snapshot, layout toggles) via explicit storage helpers, not whole stores.

### Auth boundary
- Guard protected routes with a gate component that: loads persisted token, falls back to cookie refresh bootstrap, shows loading, redirects anonymous users to login with return path.
- Centralize logout in `performSessionEnd`: API logout (best-effort) → clear auth store/storage → stop realtime connection → reset listener registration flags → navigate to login.

### Routing
- Define routes in `app/routes.tsx` with `createBrowserRouter`; mount via `RouterProvider` inside app providers.
- Structure: public login route; authenticated routes nested under gate + shell layout with `<Outlet />`.
- Use routes for major navigation; use Zustand for dense in-app state (editor tabs). If a URL param exists, keep it in sync with store or remove the param.

### Real-time
- Hold a singleton hub connection in `shared/signalr/` with token factory and reconnect handling.
- Register feature listeners once (`register*HubListeners(queryClient)`) after authentication; guard with a module-level flag reset on session end.
- Realtime events update React Query cache (`setQueryData` / `invalidateQueries`); push transient UI signals to Zustand.

### Feature hooks
- Extract repeated query+mutation+side-effect logic into `features/{Feature}/hooks/use*.ts` (debounced autosave, dirty tracking, rollback on error).
- Hooks may read/write feature stores for dirty/tab state but delegate persistence to API functions and cache updates to React Query.

### Errors and forms
- Normalize unknown errors through `toApiError()` in `shared/api/errors.ts`; surface messages via toast or form error state.
- Login and edit forms: Zod schema + react-hook-form resolver; submit handlers call feature API functions, not raw HTTP.
