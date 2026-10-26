---
tags: [supertest, testing, concept]
aliases: [app vs server, testable express app]
created: 2026-10-26
status: evergreen
---
# Supertest App Structure

> [!summary] TL;DR
> Export the app (routes + middleware) separately from the code that calls `listen()`. Tests import the app; production imports the server. Build the app through a factory to inject dependencies.

```ts
// src/app.ts
import express from "express";
import { usersRouter } from "./routes/users";
import type { Deps } from "./deps";

export function createApp(deps: Deps) {
  const app = express();
  app.use(express.json());
  app.get("/health", (_req, res) => res.json({ status: "ok" }));
  app.use("/users", usersRouter(deps));
  app.use((err, _req, res, _next) => {                      // error handler
    res.status(err.status ?? 500).json({ detail: err.message });
  });
  return app;
}

// src/server.ts
import { createApp } from "./app";
import { realDeps } from "./deps";
createApp(realDeps).listen(process.env.PORT ?? 3000);
```

```ts
// tests/users.test.ts
import request from "supertest";
import { createApp } from "../src/app";

const sendEmail = jest.fn().mockResolvedValue(undefined);
const app = createApp({ db: testDb, sendEmail });     // inject fakes, no jest.mock needed

it("sends welcome email on signup", async () => {
  await request(app).post("/users").send({ email: "a@b.co" }).expect(201);
  expect(sendEmail).toHaveBeenCalledWith("a@b.co", expect.any(String));
});
```

Works the same for Fastify (`await app.ready(); request(app.server)`) and NestJS (`request(app.getHttpServer())`).

## Related
- [[Test Doubles]] · [[FastAPI Dependency Injection]] — same idea via `dependency_overrides`
