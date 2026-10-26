---
tags: [http, concept]
aliases: [REST, RESTful, API design, pagination, versioning, problem details]
created: 2026-10-26
status: evergreen
---
# REST API Design

> [!summary] TL;DR
> Model **resources** as nouns in URLs, use HTTP **methods** for verbs, **status codes** for outcomes, and keep error/pagination formats consistent across the whole API.

## Resource naming
```
GET  /users                 ✅ plural nouns, lowercase, hyphens
GET  /users/42/orders       ✅ nesting for ownership (≤ 2 levels)
POST /orders/42/refunds     ✅ sub-resource for actions with state
GET  /getUserById?id=42     ❌ verbs in paths
```

## Filtering, sorting, sparse fields
`GET /orders?status=paid&created_after=2026-01-01&sort=-created_at&fields=id,total`

## Pagination
| Style | Request | Pros/cons |
|---|---|---|
| Offset | `?limit=20&offset=40` | simple; slow/unstable on big changing tables |
| Cursor / keyset | `?limit=20&cursor=eyJpZCI6MTAwfQ` | stable & fast; no "jump to page 7" |

Response envelope or `Link` header:
```json
{ "data": [ ... ], "next_cursor": "eyJpZCI6ODB9", "has_more": true }
```

## Errors — RFC 9457 Problem Details
```json
{
  "type": "https://api.example.com/errors/validation",
  "title": "Validation failed",
  "status": 422,
  "detail": "2 fields are invalid",
  "errors": [{ "field": "email", "message": "must be a valid email" }]
}
```

## Versioning
URL (`/v1/...`) — most explicit · header (`Accept: application/vnd.shop.v2+json`) · or evolve compatibly: add fields, never remove/rename without a new version.

## Other good practices
- Return the created resource + `201` + `Location`.
- Use ISO 8601 UTC timestamps, string IDs or UUIDs if you may change DBs.
- Consistent casing (`snake_case` or `camelCase` — pick one).
- Idempotency keys for POSTs that move money.
- Rate-limit with 429 + `Retry-After`.
- Describe it all in [[OpenAPI MOC|OpenAPI]].

## Related
- [[HTTP Methods]] · [[HTTP Status Codes]] · [[API Testing Strategy]]
- [[FastAPI MOC]] · [[Django REST Framework]] · [[TS Utility Types]]
