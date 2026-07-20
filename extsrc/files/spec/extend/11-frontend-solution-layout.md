# Frontend — solution layout

### Project structure
- Single-package SPA: `{Product}.Frontend.WebApp.{Version}` at repo root with `src/`, `public/`, configs at root.
- Split `src/` into `app/` (shell), `features/` (vertical slices), `shared/` (infrastructure).
- Use `@/` path alias mapped to `src/` in Vite and TypeScript.
- Frontend/npm apps may live beside a .NET solution but are not .NET projects in the solution file.

### Application shell (`app/`)
- Bootstrap in `src/main.tsx`: mount providers, then `RouterProvider`.
- Define all routes in `app/routes.tsx` using `createBrowserRouter`.
- Compose global providers in `app/providers.tsx` (query client, theme, toasts, i18n init).
- App chrome lives in `app/layout/`; route content renders via `<Outlet />`.
- Keep `app/` thin: providers, routing, auth gate, and persistent layout chrome only. No top-level `components/` or `pages/` folders.

### Feature slices (`features/{feature}/`)
- One folder per domain; folder name lowercase (`{feature}`).
- Each routable domain exports `{Feature}Page.tsx` as the route entry.
- Colocate feature HTTP in `api.ts`, client state in `{feature}Store.ts`, optional real-time in `signalr.ts`.
- Nested subdomains use subfolders (`editor/`, `tree/`, `hooks/`); extensibility uses a `registry/` subfolder.

### Shared layer (`shared/`)
- Single HTTP client in `shared/api/http.ts`; features never create their own clients.
- UI-facing domain types in `shared/types/`; raw wire types stay private inside feature `api.ts`.
- Design-system primitives in `shared/ui/` as kebab-case files; no feature imports inside primitives.
- Generic utilities in `shared/lib/`; generic hooks in `shared/hooks/`.
- Theme tokens in `shared/theme/`; global i18n bootstrap in `shared/i18n/`.
- Resolve env via a shared helper: runtime overlay first, then build-time `import.meta.env`.

### Naming
- Components and pages: PascalCase `.tsx`.
- Stores: `{feature}Store.ts`; hooks: `use{Purpose}.ts(x)`.
- API module always `api.ts`; query key object `{feature}QueryKeys`.
- Shared UI files: kebab-case (e.g. `alert-dialog.tsx`).

### Dependencies
- Allowed: `app` → `features` → `shared`.
- Forbidden: `shared` → `features`; feature A → feature B internals (prefer shared types or app-level composition).
