---
tags: [http, concept]
aliases: [GET POST PUT PATCH DELETE, idempotent, safe methods]
created: 2026-10-26
status: evergreen
---
# HTTP Methods

| Method | Purpose | Body | Safe | Idempotent | Typical success |
|---|---|---|---|---|---|
| `GET` | read resource/collection | no | ✅ | ✅ | 200 |
| `HEAD` | GET without body (headers only) | no | ✅ | ✅ | 200 |
| `OPTIONS` | capabilities; CORS preflight | no | ✅ | ✅ | 204 |
| `POST` | create / non-idempotent action | yes | ❌ | ❌ | 201 + `Location`, or 200/202 |
| `PUT` | replace (or create at known URI) | yes | ❌ | ✅ | 200 / 204 / 201 |
| `PATCH` | partial update | yes | ❌ | ❌* | 200 / 204 |
| `DELETE` | remove | usually no | ❌ | ✅ | 204 / 200 / 202 |

- **Safe** = no state change intended (cacheable, prefetchable).
- **Idempotent** = repeating the request has the same effect as doing it once → safe to **retry**.
- *PATCH can be made idempotent (e.g. JSON Merge Patch setting absolute values).

```
GET    /orders            list
POST   /orders            create
GET    /orders/42         read
PUT    /orders/42         replace
PATCH  /orders/42         update some fields
DELETE /orders/42         delete
POST   /orders/42/cancel  action that doesn't fit CRUD
```

> [!tip] Make POST retry-safe with an **Idempotency-Key** header: server stores the result per key and replays it for duplicates (payment APIs do this).

## Related
- [[REST API Design]] · [[HTTP Status Codes]] · [[SQL DML and Upsert]]
