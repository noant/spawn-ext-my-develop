# Frontend — tooling, build, test, and deploy

### Package manager and scripts
- Pin `packageManager` (pnpm) and declare `engines.node` / `engines.pnpm`. Use `"type": "module"`.
- Expose at minimum: `dev`, `build` (`tsc -b && vite build`), `preview`, `lint`, `typecheck`, `test`.

### TypeScript
- Root `tsconfig.json` holds path mappings and references.
- `tsconfig.app.json` covers `src/` with `strict`, `noUncheckedIndexedAccess`, `exactOptionalPropertyTypes`.
- `tsconfig.node.json` covers Vite/Vitest config files only.
- Configure `@/*` → `./src/*` in both TS `paths` and Vite/Vitest `resolve.alias`.

### Vite and Vitest
- Use `@vitejs/plugin-react` and Tailwind via `@tailwindcss/vite`.
- Separate `vitest.config.ts` with `environment: "jsdom"`, `globals: true`, and the same `@` alias.
- Keep at least one smoke test under `src/__smoke__/`.

### ESLint
- Single flat `eslint.config.js`: `@eslint/js` + `typescript-eslint` + React Hooks + React Refresh + `eslint-config-prettier`.
- Lint only `src/**/*.{ts,tsx}`; ignore `dist`, `node_modules`, `coverage`.

### Environment variables
- All client env keys use `VITE_` prefix, declared once in `src/vite-env.d.ts` (`ImportMetaEnv`).
- Application code reads env only through a shared `runtimeEnv(key)` helper.
- Dev/build: Vite injects `import.meta.env` from `.env` / `.env.local` / `.env.production`.
- Production container: entrypoint generates `/env.js` setting `window.__ENV__` from runtime vars; `index.html` loads `env.js` before the app bundle; `public/env.js` provides an empty dev stub.
- Commit a baseline `.env` with local defaults; gitignore `.env.local` and `.env.*.local`.

### Docker static SPA
- Dockerfile copies prebuilt `dist/` into `nginx:alpine`.
- `nginx.conf` uses `try_files … /index.html` for client routing.
- Do not run Node in the production image unless SSR is required.

### Gitignore
- Ignore `node_modules/`, `dist/`, `coverage/`, `.vite/`, `*.tsbuildinfo`.
