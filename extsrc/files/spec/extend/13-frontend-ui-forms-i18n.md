# Frontend — UI, forms, layout, and i18n

### Feature-sliced UI
- Put route screens, feature components, API modules, and feature stores under `src/features/{Feature}/`.
- Do not use top-level `components/` or `pages/` folders when this layout is adopted.
- Keep `src/app/` thin: providers, routing, auth gate, and persistent layout chrome only.

### Design system
- Install shadcn/ui into `shared/ui`; configure `components.json` aliases to point at `shared/`.
- Extend primitives there (pane header bar, pane tab variants) rather than forking per feature.
- Style with Tailwind utilities and CSS-variable tokens; compose classes with `cn()` (`clsx` + `tailwind-merge`).
- Use CVA for variant-driven shared components: Radix primitive + `cva` variants + `forwardRef` + optional `asChild`.

### Forms
- Standardize on react-hook-form + Zod + shadcn Form.
- Colocate schema with the form; use `FormField` render props.
- Map API DTOs with explicit `toFormValues` / `toPayload` helpers when wire and UI shapes differ.

### IDE-style layouts
- Shell = column (top bar → horizontal resizable `PanelGroup` → status bar).
- Main content via router `<Outlet />`.
- Persist pane sizes/visibility with stable `autoSaveId` and optional localStorage for toggle state.
- Reuse screens across routes and panes via thin slot components (`{Feature}TabSlot` re-exports `{Feature}Page`).

### Pluggable subsystems
- Complex features use a registry (`descriptors.ts` + `resolve{Handler}.ts`) with capability flags.
- New variants extend the registry; page/store/api boundaries stay unchanged.

### Internationalization
- User-visible strings via `react-i18next`; JSON files under `public/locales/{lang}/` aligned to feature areas.
- Components use `useTranslation("{namespace}")`; no hardcoded user-facing strings.
- Bridge third-party widget CSS to design tokens in global CSS, not one-off component hacks.
