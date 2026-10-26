---
tags: [http, concept]
aliases: [HTTP message, request line, URL anatomy]
created: 2026-10-26
status: evergreen
---
# HTTP Request and Response Anatomy

```http
POST /api/v1/orders?dry_run=true HTTP/1.1          ← method, path + query, version
Host: shop.example.com                              ← headers
Content-Type: application/json
Authorization: Bearer eyJhbGciOi...
Accept: application/json
Content-Length: 41
                                                    ← blank line
{"items":[{"sku":"PEN-1","quantity":2}]}           ← body
```

```http
HTTP/1.1 201 Created                                ← version, status code, reason
Content-Type: application/json
Location: /api/v1/orders/9481
Cache-Control: no-store

{"id":9481,"status":"pending","total":3.00}
```

## URL anatomy
```
https://user:pw@api.example.com:8443/v1/users/42?fields=name,email&sort=-created#top
└─┬─┘   └──┬──┘ └──────┬──────┘└─┬─┘└────┬─────┘└──────────────┬──────────────┘└┬┘
scheme  userinfo     host      port   path               query               fragment (never sent to server)
```
Percent-encode reserved characters in query values (`space` → `%20` or `+` in forms).

## Versions
- **HTTP/1.1** — text, one request at a time per connection (keep-alive).
- **HTTP/2** — binary, multiplexed streams over one TCP connection, header compression.
- **HTTP/3** — over QUIC (UDP), no head-of-line blocking.

See it live: `curl -v https://example.com` → [[curl Debugging and Timing]].

## Related
- [[HTTP Methods]] · [[HTTP Headers]] · [[HTTP Status Codes]]
