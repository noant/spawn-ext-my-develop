---
name: feature-fullstack
description: Scaffold a full-stack feature (backend + frontend).
---

1. Backend half:
   - Define the abstractions layer for the feature.
   - Add the implementation project and register it in DI.
   - Expose the feature via endpoints; add persistence or workers when needed.
2. Frontend half:
   - Create the feature slice in the frontend solution layout.
   - Add the page, API layer, and state per the data/state rules.
   - Cover forms, i18n, and domain error handling.
3. Keep naming and API contracts consistent across both halves: one `{Feature}` name end to end, frontend wire types mirror the API records, domain errors surface through the frontend error pipeline.
4. Follow the mandatory reads for concrete patterns and conventions.
