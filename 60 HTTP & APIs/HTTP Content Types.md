---
tags: [http, concept]
aliases: [MIME types, multipart form-data, urlencoded]
created: 2026-10-26
status: evergreen
---
# HTTP Content Types

| Content-Type | Body looks like | Used for |
|---|---|---|
| `application/json` | `{"name":"Ada","tags":["x"]}` | APIs |
| `application/x-www-form-urlencoded` | `name=Ada&tags=x&tags=y` | HTML forms, OAuth2 token endpoint |
| `multipart/form-data; boundary=…` | parts with headers | file uploads + fields |
| `text/plain`, `text/html`, `text/csv` | text | |
| `application/octet-stream` | raw bytes | binary download |
| `application/problem+json` | RFC 9457 error object | API errors |
| `application/x-ndjson` / `text/event-stream` | line-delimited / SSE | streaming |

## Same request in each client
| | JSON | Form | Multipart |
|---|---|---|---|
| curl | `--json '{"a":1}'` | `-d a=1` | `-F a=1 -F file=@x.png` |
| HTTPie | `http POST url a=1` | `http -f POST url a=1` | `http -f POST url file@x.png` |
| Supertest | `.send({a:1})` | `.type("form").send({a:1})` | `.field("a","1").attach("file","x.png")` |
| Playwright | `{ data: {a:1} }` | `{ form: {a:1} }` | `{ multipart: {...} }` |
| PowerShell | `-Body ($o \| ConvertTo-Json) -ContentType application/json` | `-Body @{a=1}` | `-Form @{file=Get-Item x.png}` |
| FastAPI | Pydantic body | `Form()` | `UploadFile = File()` |

## Related
- [[HTTP Headers]] · [[curl Sending Data]] · [[HTTPie Request Items]]
