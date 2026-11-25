# spawn-ext-my-develop

Personal development methodology pack for [Spawn](https://github.com/noant/spawn): agent rules, C#/.NET + React/Vite conventions, and feature-scaffolding skills for the AI-assisted workflow.

## What you get

- Global agent rules: concise structured replies and docs, definitions for key terms inline, build + syntax checks + tests after code edits.
- Backend conventions: C# fluent style, DI, EF Core, WebApi minimal endpoints, processors/workers/jobs, domain errors.
- Frontend conventions: SPA layout, React Query + Zustand, forms and i18n, domain error handling, UI design quality bar.
- Skills:
  - `commit-push` — commit and push changes in a git session repo.
  - `feature-fullstack` — scaffold a full-stack feature end to end.
  - `feature-backend` — backend half: abstractions, implementation, DI, endpoints.
  - `feature-frontend` — frontend half: page, API layer, state, i18n.

Skills mark the relevant conventions as mandatory reads, so the agent applies them automatically.

## Install

In the target repository root:

```
spawn extension add https://github.com/noant/spawn-ext-my-develop.git
```

After install, conventions land under `spec/extend/` and skills appear in the IDE; updates via `spawn extension update`.

## Requirements

- Spawn CLI installed and `spawn init` done in the target repo.