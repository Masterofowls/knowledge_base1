---
tags: [openapi, concept]
aliases: [openapi.yaml, openapi skeleton]
created: 2026-10-26
status: evergreen
---
# OpenAPI Document Structure

```yaml
openapi: 3.1.0
info:
  title: Shop API
  version: 1.4.0
  description: |
    Orders and products. **Markdown** supported.
  contact: { name: API Team, email: api@example.com }
servers:
  - url: https://api.example.com/v1
    description: Production
  - url: http://localhost:8000
    description: Local
tags:
  - name: items
    description: Product catalogue
paths:            # → [[OpenAPI Paths and Operations]]
  /items: { ... }
components:       # → [[OpenAPI Schemas and Components]]
  schemas: { ... }
  parameters: { ... }
  responses: { ... }
  securitySchemes: { ... }
security:         # global default → [[OpenAPI Security Schemes]]
  - bearerAuth: []
webhooks: { ... } # 3.1: callbacks you send to subscribers
```

| Version | Notes |
|---|---|
| Swagger 2.0 | `swagger: "2.0"`, `definitions`, `host/basePath` |
| OAS 3.0 | `components`, `servers`, `requestBody`, `nullable: true` |
| **OAS 3.1** | full JSON Schema: `type: [string, "null"]`, `examples` array, `webhooks` |

> [!tip] Split big specs into files and `$ref` them (`$ref: './schemas/Item.yaml'`); bundle with `redocly bundle` before publishing.

## Related
- [[OpenAPI MOC]] · [[OpenAPI Tooling]]
