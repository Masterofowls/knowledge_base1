---
tags: [supertest, testing, snippet]
aliases: [supertest request]
created: 2026-10-26
status: evergreen
---
# Supertest Basics

```bash
npm i -D supertest @types/supertest jest
```

```ts
import request from "supertest";
import { app } from "../src/app";        // the Express app, NOT a listening server

describe("GET /health", () => {
  it("returns ok", async () => {
    const res = await request(app).get("/health");
    expect(res.status).toBe(200);
    expect(res.body).toEqual({ status: "ok" });
  });
});

describe("POST /users", () => {
  it("creates a user", async () => {
    const res = await request(app)
      .post("/users")
      .send({ email: "ada@example.com", name: "Ada" })   // object → JSON, sets Content-Type
      .set("Accept", "application/json")
      .expect("Content-Type", /json/)
      .expect(201);

    expect(res.body).toMatchObject({ id: expect.any(Number), email: "ada@example.com" });
    expect(res.headers.location).toBe(`/users/${res.body.id}`);
  });
});
```

`request(app)` binds the app to an **ephemeral port** for the request and closes it — no port conflicts, no `listen()` needed. You can also pass a URL: `request("http://localhost:3000")`.

## Request builder
`.get/.post/.put/.patch/.delete(path)` · `.query({ page: 2 })` · `.send(body)` · `.set(header, value)` · `.auth(user, pass)` / `.auth(token, { type: "bearer" })` · `.attach("file", path)` · `.field("name", "x")` · `.type("form")` · `.timeout(5000)` · `.redirects(1)`

## Related
- [[Supertest Assertions]] · [[Supertest App Structure]] · [[Jest Basics]]
