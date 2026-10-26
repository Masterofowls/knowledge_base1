---
tags: [openapi, comparison]
aliases: [API-first, spec-first, code-first]
created: 2026-10-26
status: evergreen
---
# OpenAPI Design-First vs Code-First

| | Design-first (spec-first) | Code-first |
|---|---|---|
| Source of truth | hand-written `openapi.yaml` | annotations/types in code |
| Examples | Stoplight, Redocly, any editor + Spectral | [[FastAPI and OpenAPI\|FastAPI]], drf-spectacular, NestJS Swagger, tsoa |
| Pros | review the contract before coding; parallel FE/BE work via mocks; language-neutral | spec never drifts from implementation; less duplication |
| Cons | must enforce implementation matches (contract tests, generated server stubs) | design discussions happen in code review; spec quality depends on annotations |

**Hybrid that works well:** code-first generation + commit the generated `openapi.json` + CI step that fails if it changes unexpectedly (`oasdiff breaking old.json new.json`) + lint with Spectral.

## Related
- [[OpenAPI Tooling]] · [[REST API Design]]
