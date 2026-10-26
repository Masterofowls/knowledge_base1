---
tags: [http, guide]
aliases: [Learn HTTP, HTTP roadmap]
created: 2026-10-26
---
# HTTP Getting Started

## 1. See HTTP with your own eyes
- Browser DevTools (`F12`) → **Network** tab → reload any page → click a request → Headers / Preview / Timing.
- Terminal: `curl -v https://example.com`
- Echo service: `curl https://httpbin.org/anything -d hello=world`

## 2. Learning checklist
- [ ] Message anatomy & URLs → [[HTTP Request and Response Anatomy]]
- [ ] Methods, safety, idempotency → [[HTTP Methods]]
- [ ] Status codes → [[HTTP Status Codes]]
- [ ] Headers & caching → [[HTTP Headers]]
- [ ] Content types → [[HTTP Content Types]]
- [ ] REST design → [[REST API Design]]
- [ ] Authentication → [[Authentication Schemes]]
- [ ] CORS → [[CORS]]
- [ ] Clients → [[curl Getting Started]], [[HTTPie Getting Started]], [[PowerShell Web Requests]]
- [ ] Contracts → [[OpenAPI Getting Started]]
- [ ] Practise: [[Tutorial - Exploring an API with curl and HTTPie]]

## 3. Self-check questions
> [!question]- What's the difference between 401 and 403?
> 401 = not authenticated (who are you?); 403 = authenticated but not allowed.

> [!question]- Why is PUT idempotent but POST isn't?
> PUT sets a resource to a given state — repeating it gives the same state. POST typically creates something new each time.

> [!question]- Why does my API work in curl but fail in the browser?
> Most likely CORS: browsers enforce it, curl doesn't. Check the preflight `OPTIONS` response.

> [!question]- When is a response cached?
> When `Cache-Control`/`Expires` allow it; `ETag` + `If-None-Match` enable cheap revalidation (304).

## Resources
- MDN HTTP docs — developer.mozilla.org/docs/Web/HTTP
- httpstatuses / http.cat for quick lookup
- RFC 9110 (HTTP Semantics) when you need the authoritative answer
