---
name: new-frontend-feature
description: Scaffold a frontend feature slice with API, store, and page.
---

1. Read `spec/extend/11-frontend-solution-layout.md`, `spec/extend/12-frontend-data-state-routing.md`, `spec/extend/13-frontend-ui-forms-i18n.md`.
2. Create `src/features/{feature}/` (lowercase domain name).
3. Add `{Feature}Page.tsx` as route entry; register route in `app/routes.tsx` under auth gate + shell.
4. Add `api.ts`: fetch functions, `{feature}QueryKeys`, wire→UI mappers (`map*WireToUi` / `map*UiToWire`).
5. Add `{feature}Store.ts` with Zustand only for UI/ephemeral state (not server lists).
6. Optional: `signalr.ts` with `register{Feature}HubListeners(queryClient)`; hooks in `hooks/use*.ts`.
7. Use shared HTTP client and types; no raw axios in components; i18n namespace in `public/locales/`.
