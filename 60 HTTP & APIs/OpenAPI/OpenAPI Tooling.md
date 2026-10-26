---
tags: [openapi, testing, snippet]
aliases: [Swagger UI, Redoc, Spectral, Prism, Schemathesis, openapi-generator]
created: 2026-10-26
status: evergreen
---
# OpenAPI Tooling

| Need | Tool | Command |
|---|---|---|
| Interactive docs | Swagger UI, Redoc, Scalar | FastAPI serves `/docs`, `/redoc` |
| Lint / style guide | **Spectral**, Redocly CLI | `npx @stoplight/spectral-cli lint openapi.yaml` |
| Validate & bundle | Redocly CLI | `npx @redocly/cli lint openapi.yaml` · `bundle -o dist.yaml` |
| Mock server | **Prism** | `npx @stoplight/prism-cli mock openapi.yaml` → :4010 |
| Breaking-change diff | oasdiff | `oasdiff breaking old.yaml new.yaml` |
| TS types / client | openapi-typescript, @hey-api/openapi-ts, Orval | `npx openapi-typescript openapi.yaml -o src/api.d.ts` |
| Multi-language clients/servers | openapi-generator | `npx @openapitools/openapi-generator-cli generate -i openapi.yaml -g python -o client/` |
| Property-based contract testing | **Schemathesis** | `schemathesis run http://localhost:8000/openapi.json --checks all` |
| Response validation in tests | `jest-openapi`, `openapi-core` (Python) | assert responses match spec |
| Import to clients | Postman, Insomnia, Bruno, HTTPie Desktop | import URL/file |

```bash
# Prism as a validation proxy in front of the real API: flags any response that violates the spec
npx @stoplight/prism-cli proxy openapi.yaml http://localhost:8000 --errors
```

```ts
// openapi-typescript + openapi-fetch: fully typed fetch from the spec
import createClient from "openapi-fetch";
import type { paths } from "./api";
const client = createClient<paths>({ baseUrl: "http://localhost:8000" });
const { data, error } = await client.GET("/items/{itemId}", { params: { path: { itemId: 1 } } });
```

## Related
- [[API Testing Strategy]] · [[FastAPI and OpenAPI]] · [[TS Runtime Validation]] · [[pytest Plugins]]
