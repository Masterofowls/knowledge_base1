---
tags: [http, security, concept]
aliases: [cross-origin, preflight, Access-Control-Allow-Origin]
created: 2026-10-26
status: evergreen
---
# CORS

> [!summary] TL;DR
> The **Same-Origin Policy** stops browser JS on origin A from reading responses from origin B. **CORS** headers from B tell the browser which origins may. It's enforced by **browsers only** — curl, Postman, and servers ignore it.

Origin = scheme + host + port. `http://localhost:5173` ≠ `http://localhost:8000`.

## Simple vs preflighted
Requests with custom headers (e.g. `Authorization`), JSON `Content-Type`, or methods other than GET/HEAD/POST trigger a **preflight**:

```http
OPTIONS /api/orders HTTP/1.1
Origin: http://localhost:5173
Access-Control-Request-Method: POST
Access-Control-Request-Headers: content-type, authorization

HTTP/1.1 204 No Content
Access-Control-Allow-Origin: http://localhost:5173
Access-Control-Allow-Methods: GET, POST, PATCH, DELETE
Access-Control-Allow-Headers: content-type, authorization
Access-Control-Allow-Credentials: true
Access-Control-Max-Age: 600
Vary: Origin
```

## Server config
- FastAPI: `CORSMiddleware(allow_origins=[...], allow_credentials=True, ...)` → [[FastAPI Basics]]
- Django: `django-cors-headers` → [[Django Settings and Middleware]]
- Express: `app.use(cors({ origin: [...], credentials: true }))`

> [!warning] `Access-Control-Allow-Origin: *` cannot be combined with credentials (cookies/auth). List explicit origins.

> [!tip] Dev shortcut: proxy API calls through the frontend dev server (Vite `server.proxy`) → same origin, no CORS.

## Debug
```bash
curl -i -X OPTIONS http://localhost:8000/api/orders \
  -H "Origin: http://localhost:5173" -H "Access-Control-Request-Method: POST"
```

## Related
- [[HTTP Headers]] · [[Authentication Schemes]]
