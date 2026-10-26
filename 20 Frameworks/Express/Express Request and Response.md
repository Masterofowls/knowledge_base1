---
tags: [express, snippet]
aliases: [req res, express request object, res.json]
created: 2026-10-26
status: evergreen
---
# Express Request and Response

## `req`
| Property | Contains |
|---|---|
| `req.params` | path params `{ id: "42" }` |
| `req.query` | query string `{ page: "2" }` (strings / arrays) |
| `req.body` | parsed body (needs `express.json()` / `express.urlencoded()`) |
| `req.headers` / `req.get("Authorization")` | headers (lower-cased keys) |
| `req.cookies` | needs `cookie-parser` |
| `req.method`, `req.path`, `req.originalUrl` | |
| `req.ip`, `req.ips` | client IP (set `app.set("trust proxy", 1)` behind a proxy) |
| `req.is("application/json")` | content-type check |
| `req.accepts(["json", "html"])` | content negotiation |

## `res`
```js
res.status(201).json({ id: 1 });          // JSON + Content-Type
res.send("text or Buffer");
res.sendStatus(204);                       // status + default body
res.set("X-Request-Id", id);               // header
res.location("/users/1");
res.redirect(303, "/login");
res.cookie("sid", token, { httpOnly: true, secure: true, sameSite: "lax", maxAge: 3600_000 });
res.clearCookie("sid");
res.download("./reports/q3.pdf");
res.sendFile(path.resolve("public/index.html"));
res.type("text/csv").send(csv);
res.format({ json: () => res.json(data), html: () => res.render("page", data) });
res.locals.user = user;                    // per-request data for later middleware/views
```

> [!warning] Send exactly **one** response. Calling `res.json()` twice → `ERR_HTTP_HEADERS_SENT`. Use `return res.status(404).json(...)` to stop early.

## Related
- [[HTTP Headers]] · [[HTTP Status Codes]] · [[HTTP Content Types]]
