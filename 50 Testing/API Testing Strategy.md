---
tags: [testing, http, concept]
aliases: [API testing, what to test in an API]
created: 2026-10-26
status: evergreen
---
# API Testing Strategy

## Checklist per endpoint
- ✅ **Happy path** — correct status ([[HTTP Status Codes]]), body shape, headers (`Content-Type`, `Location` on 201).
- ✅ **Validation** — missing/extra/wrong-type fields → 400/422 with useful errors.
- ✅ **Auth** — no token → 401; wrong role/other user's resource → 403/404.
- ✅ **Not found** — unknown IDs → 404.
- ✅ **Conflicts** — duplicates → 409; optimistic-lock mismatch → 409/412.
- ✅ **Idempotency** — PUT/DELETE repeated give same result ([[HTTP Methods]]).
- ✅ **Pagination, filtering, sorting** — boundaries (empty, one page, last page).
- ✅ **Side effects** — DB state changed, event/email emitted.
- ✅ **Contract** — response matches the [[OpenAPI MOC|OpenAPI]] schema.
- ✅ **Non-functional** — rate limiting (429), timeouts, payload size limits, CORS.

## Layers & tools
| Layer | Tool |
|---|---|
| In-process (fast, no network) | [[FastAPI Testing\|TestClient]], [[Django Testing\|Django client]], [[Supertest MOC\|Supertest]] |
| Against a running server | [[Playwright API Testing]], pytest + httpx |
| Exploratory/manual | [[curl MOC\|curl]], [[HTTPie MOC\|HTTPie]], Postman/Bruno |
| Contract / fuzz | Schemathesis, Dredd, Pact — see [[OpenAPI Tooling]] |
| Load | k6, Locust |

## Related
- [[REST API Design]] · [[Testing Pyramid]]
