---
tags: [express, concept]
aliases: [express app, app.listen]
created: 2026-10-26
status: evergreen
---
# Express Basics

> [!summary] TL;DR
> An Express app is a **pipeline of middleware and route handlers**. Each incoming request flows through them in the order they were registered until one sends a response.

```js
// server.js  (package.json: "type": "module")
import express from "express";

const app = express();
app.use(express.json());                       // parse JSON bodies into req.body

app.get("/health", (req, res) => {
  res.json({ status: "ok" });
});

app.post("/echo", (req, res) => {
  res.status(201).json({ youSent: req.body });
});

app.use((req, res) => res.status(404).json({ detail: "Not found" }));   // fallthrough

const port = process.env.PORT ?? 3000;
app.listen(port, () => console.log(`Listening on http://localhost:${port}`));
```

```bash
npm i express
node --watch server.js              # Node 18.11+: restart on change (or nodemon)
curl localhost:3000/health
curl --json '{"a":1}' localhost:3000/echo
```

## Request lifecycle
```
request → app.use(json) → app.use(logger) → router match → handler → res.json()
                                                       ↘ next(err) → error middleware
```

## Express 5 changes worth knowing
- Rejected promises / thrown errors in `async` handlers are forwarded to error middleware automatically.
- Path syntax: wildcards must be named (`/files/*path`), optional segments use braces (`/:file{.:ext}`).
- `req.query` is read-only (getter); `res.status()` validates codes.
- Removed: `app.del`, `res.send(status)`, `req.param()`.

## Related
- [[Express Routing]] · [[Express Middleware]] · [[HTTP Request and Response Anatomy]]
