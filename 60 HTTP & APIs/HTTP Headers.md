---
tags: [http, concept]
aliases: [headers, caching headers, ETag, Cache-Control]
created: 2026-10-26
status: evergreen
---
# HTTP Headers

## Content negotiation
| Header | Example |
|---|---|
| `Content-Type` | `application/json; charset=utf-8` — what **this** body is |
| `Accept` | `application/json` — what I want back |
| `Accept-Encoding` / `Content-Encoding` | `gzip, br` |
| `Accept-Language` | `en-GB,en;q=0.9` |

## Auth & identity
`Authorization: Bearer <token>` · `Authorization: Basic base64(user:pass)` · `X-API-Key: ...` · `Cookie` / `Set-Cookie` · `WWW-Authenticate` (on 401)

## Caching
```http
Cache-Control: public, max-age=3600          # cache for 1h
Cache-Control: no-store                      # never cache (sensitive data)
Cache-Control: no-cache                      # cache but revalidate every time
ETag: "v42-abc"                              # version fingerprint
If-None-Match: "v42-abc"                     # → 304 Not Modified if unchanged
Last-Modified / If-Modified-Since
Vary: Accept-Encoding, Authorization         # cache key varies by these headers
```

## Concurrency control
`If-Match: "v42-abc"` on PUT/PATCH → 412 if someone else changed it (optimistic locking, see [[SQL Transactions and Isolation]]).

## Other common ones
| Header | Purpose |
|---|---|
| `Location` | URL of created resource (201) / redirect target (3xx) |
| `Retry-After` | seconds/date to wait (429, 503) |
| `X-Request-ID` / `traceparent` | request correlation / distributed tracing |
| `RateLimit-Limit` / `RateLimit-Remaining` | rate limit info |
| `Link` | pagination: `<…?page=3>; rel="next"` |
| `Idempotency-Key` | safe POST retries |

## Security headers
`Strict-Transport-Security`, `Content-Security-Policy`, `X-Content-Type-Options: nosniff`, `X-Frame-Options` / CSP `frame-ancestors`, `Referrer-Policy`, `Set-Cookie: …; HttpOnly; Secure; SameSite=Lax`.

## Related
- [[CORS]] · [[HTTP Content Types]] · [[Authentication Schemes]] · [[curl Headers and Auth]]
