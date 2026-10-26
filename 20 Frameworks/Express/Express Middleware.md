---
tags: [express, concept]
aliases: [middleware, next(), app.use]
created: 2026-10-26
status: evergreen
---
# Express Middleware

> [!summary] TL;DR
> Middleware is a function `(req, res, next)`. It can read/modify `req`/`res`, end the request by sending a response, or call `next()` to pass control on. Error middleware has **four** args: `(err, req, res, next)`.

```js
// Logger with timing
function requestLogger(req, res, next) {
  const start = performance.now();
  res.on("finish", () => {
    console.log(`${req.method} ${req.originalUrl} ${res.statusCode} ${(performance.now() - start).toFixed(1)}ms`);
  });
  next();
}

// Request ID
import { randomUUID } from "node:crypto";
const requestId = (req, res, next) => {
  req.id = req.get("X-Request-Id") ?? randomUUID();
  res.set("X-Request-Id", req.id);
  next();
};

// Middleware factory (configurable)
const requireRole = (role) => (req, res, next) =>
  req.user?.role === role ? next() : res.status(403).json({ detail: "Forbidden" });

app.use(requestId, requestLogger);              // all routes
app.use("/admin", authenticate, requireRole("admin"), adminRouter);   // path-scoped
app.get("/reports", authenticate, getReports);  // route-scoped
```

## Built-in & common middleware
| Middleware | Purpose |
|---|---|
| `express.json({ limit: "1mb" })` | JSON body |
| `express.urlencoded({ extended: true })` | form bodies |
| `express.static("public")` | static files |
| `cors()` | [[CORS]] |
| `helmet()` | security headers |
| `morgan("dev")` / `pino-http` | request logging |
| `cookie-parser` | `req.cookies` |
| `compression()` | gzip |
| `express-rate-limit` | throttling |
| `multer` | multipart uploads |

## Order rules
1. Body parsers & security headers first.
2. Auth before protected routers.
3. Routes.
4. 404 handler.
5. Error handler **last**.

## Related
- [[Express Error Handling]] · [[Django Settings and Middleware]] (same onion idea) · [[FastAPI Dependency Injection]] (FastAPI's equivalent for per-route concerns)
