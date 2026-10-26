---
tags: [express, concept]
aliases: [error middleware, async errors express]
created: 2026-10-26
status: evergreen
---
# Express Error Handling

```js
// errors.js
export class HttpError extends Error {
  constructor(status, message, details) {
    super(message);
    this.status = status;
    this.details = details;
  }
}
export const notFound = (what) => new HttpError(404, `${what} not found`);

// in a route (Express 5 forwards thrown/rejected errors automatically)
router.get("/:id", async (req, res) => {
  const item = await items.get(Number(req.params.id));
  if (!item) throw notFound("Item");
  res.json(item);
});

// 404 for unknown routes
app.use((req, res) => res.status(404).json({ detail: `No route ${req.method} ${req.path}` }));

// error handler — 4 args, registered LAST
app.use((err, req, res, next) => {
  const status = err.status ?? 500;
  if (status >= 500) req.log?.error(err) ?? console.error(err);
  res.status(status).type("application/problem+json").json({
    title: status >= 500 ? "Internal Server Error" : err.message,
    status,
    detail: status >= 500 ? undefined : err.details,    // never leak stack traces
    instance: req.originalUrl,
  });
});
```

> [!warning] Express 4 does **not** catch rejected promises — wrap handlers (`express-async-errors` or `const wrap = fn => (req, res, next) => fn(req, res, next).catch(next)`). Express 5 does this for you.

## Process-level safety
```js
process.on("unhandledRejection", (r) => { console.error(r); process.exit(1); });
const server = app.listen(port);
process.on("SIGTERM", () => server.close(() => process.exit(0)));   // graceful shutdown
```

## Related
- [[JS Error Handling]] · [[FastAPI Error Handling]] · [[REST API Design]] (Problem Details) · [[HTTP Status Codes]]
