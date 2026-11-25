# spawn-ext-my-develop

Spawn extension with personal development methodology: global agent rules, C#/.NET + React/Vite conventions, and feature-scaffolding skills.

## Install

```
spawn extension add <repo-url>
```

Installs into `spawn/.extend/my-develop/` of the target repo; files materialize under `spec/`.

## Contents

- `extsrc/config.yaml` — extension manifest: global hints, files, skills, ignores.
- `extsrc/files/spec/extend/` — conventions:
  - C# style, solution layout, EF Core, HttpClient, abstractions, DI, WebApi, processors/workers/jobs, implementation projects
  - Frontend: solution layout, data/state/routing, UI/forms/i18n, tooling, domain errors (backend + frontend), UI design
- `extsrc/skills/`:
  - `commit-push` — commit and push in a git session repo
  - `feature-fullstack` — scaffold a full-stack feature (backend + frontend)
  - `feature-backend` — abstractions, implementation, DI, endpoints
  - `feature-frontend` — page, API layer, state, i18n
  - Skills carry `required-read` rule sets; the agent reads them as mandatory context.

## Conventions

- Chat in Russian; specs, comments, logs in English; ASCII where practical.
- Concise replies/docs; lists over paragraphs; short definitions inline.
- Build, syntax checks, and tests after code edits and task completion.

## Versioning

Semantic versioning in `extsrc/config.yaml`; bump before publishing (MAJOR on removed/renamed surfaces).

## Development

1. Edit `extsrc/` (config, files, skills).
2. Validate: `spawn extension check . --strict`.
3. Bump version, commit, push.
