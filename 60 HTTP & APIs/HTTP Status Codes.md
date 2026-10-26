---
tags: [http, concept]
aliases: [status codes, 404, 401 vs 403, 422]
created: 2026-10-26
status: evergreen
---
# HTTP Status Codes

| Class | Meaning |
|---|---|
| 1xx | informational |
| **2xx** | success |
| **3xx** | redirection |
| **4xx** | client error — fix the request |
| **5xx** | server error — not the client's fault |

## The ones you'll actually use
| Code | Name | When |
|---|---|---|
| 200 | OK | successful GET/PUT/PATCH with body |
| 201 | Created | POST created a resource (+ `Location` header) |
| 202 | Accepted | queued for async processing |
| 204 | No Content | success, empty body (DELETE, some PUT) |
| 301 / 308 | Moved Permanently / Permanent Redirect | URL changed forever (308 keeps method) |
| 302 / 307 | Found / Temporary Redirect | temporary (307 keeps method) |
| 303 | See Other | redirect to GET after POST (PRG pattern) |
| 304 | Not Modified | conditional GET, cache still valid |
| 400 | Bad Request | malformed syntax / generic validation |
| **401** | Unauthorized | **not authenticated** (missing/invalid credentials) + `WWW-Authenticate` |
| **403** | Forbidden | **authenticated but not allowed** |
| 404 | Not Found | no such resource (also used to hide existence) |
| 405 | Method Not Allowed | + `Allow` header |
| 406 | Not Acceptable | can't satisfy `Accept` |
| 409 | Conflict | duplicate, state conflict, version mismatch |
| 410 | Gone | permanently removed |
| 412 | Precondition Failed | `If-Match` ETag mismatch |
| 413 | Content Too Large | body too big |
| 415 | Unsupported Media Type | wrong `Content-Type` |
| **422** | Unprocessable Content | well-formed but semantically invalid (FastAPI validation) |
| 429 | Too Many Requests | rate limited (+ `Retry-After`) |
| 500 | Internal Server Error | unhandled exception |
| 502 | Bad Gateway | upstream returned garbage |
| 503 | Service Unavailable | overloaded / maintenance (+ `Retry-After`) |
| 504 | Gateway Timeout | upstream too slow |

> [!tip] Retry only on: network errors, 408, 429, 502, 503, 504 — with exponential backoff + jitter, and only for idempotent requests ([[HTTP Methods]]).

## Related
- [[REST API Design]] · [[FastAPI Error Handling]] · [[API Testing Strategy]] · [[JS Error Handling]] (`fetch` doesn't throw on 4xx/5xx)
