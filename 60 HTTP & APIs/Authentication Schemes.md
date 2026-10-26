---
tags: [http, security, concept]
aliases: [auth, JWT, OAuth2, API keys, sessions, bearer token]
created: 2026-10-26
status: evergreen
---
# Authentication Schemes

> [!summary] TL;DR
> **Authentication** = who are you (401 if unknown). **Authorization** = what may you do (403 if not allowed).

| Scheme | How it's sent | Good for | Watch out |
|---|---|---|---|
| **Session cookie** | `Cookie: sessionid=…` (server stores session) | same-site web apps | CSRF → `SameSite`, CSRF tokens |
| **Basic** | `Authorization: Basic base64(user:pass)` | internal tools, tests | only over HTTPS; creds every request |
| **API key** | `X-API-Key: …` or query param | server-to-server, public APIs | no user identity; rotate; never in URLs in logs |
| **Bearer / JWT** | `Authorization: Bearer eyJ…` | SPAs, mobile, microservices | can't revoke easily → short expiry + refresh tokens |
| **OAuth 2.0 / OIDC** | flows that issue bearer tokens | "Sign in with…", delegated access | use Authorization Code + PKCE for public clients |
| **mTLS** | client certificate | service mesh, B2B | cert management |

## JWT anatomy
`header.payload.signature` — base64url-encoded, **signed not encrypted**.
```json
{ "sub": "42", "role": "admin", "iat": 1761480000, "exp": 1761480900, "iss": "https://auth.example.com" }
```
Verify signature, `exp`, `iss`, `aud`; pin the algorithm.

## OAuth 2.0 flows (pick one)
- **Authorization Code + PKCE** — browsers/mobile apps on behalf of a user.
- **Client Credentials** — machine-to-machine.
- ~~Implicit~~, ~~Password~~ — deprecated (password flow still appears in FastAPI tutorials for simplicity).

## Where it shows up in this vault
- Server: [[FastAPI Authentication]] · [[Django Authentication]]
- Spec: [[OpenAPI Security Schemes]]
- Clients: [[curl Headers and Auth]] · [[HTTPie Authentication and Sessions]] · [[PowerShell Web Requests]]
- Tests: [[Playwright Authentication]] · [[Supertest Authentication and Agents]]
