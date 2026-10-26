---
tags: [openapi, security, snippet]
aliases: [securitySchemes, bearerAuth, apiKey openapi]
created: 2026-10-26
status: evergreen
---
# OpenAPI Security Schemes

```yaml
components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
    basicAuth:
      type: http
      scheme: basic
    apiKeyHeader:
      type: apiKey
      in: header
      name: X-API-Key
    sessionCookie:
      type: apiKey
      in: cookie
      name: sessionid
    oauth:
      type: oauth2
      flows:
        authorizationCode:
          authorizationUrl: https://auth.example.com/authorize
          tokenUrl: https://auth.example.com/token
          scopes:
            items:read: Read items
            items:write: Modify items
    oidc:
      type: openIdConnect
      openIdConnectUrl: https://auth.example.com/.well-known/openid-configuration

security:                      # global: bearer OR api key
  - bearerAuth: []
  - apiKeyHeader: []

paths:
  /health:
    get:
      security: []             # public endpoint overrides global
  /items:
    post:
      security:
        - oauth: [items:write]
```

Semantics: list entries are **OR**; multiple schemes inside one entry are **AND**.

## Related
- [[Authentication Schemes]] · [[FastAPI Authentication]] (auto-registers `OAuth2PasswordBearer`)
