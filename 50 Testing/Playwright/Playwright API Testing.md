---
tags: [playwright, testing, http, snippet]
aliases: [APIRequestContext, request fixture]
created: 2026-10-26
status: evergreen
---
# Playwright API Testing

> [!summary] TL;DR
> The `request` fixture (an `APIRequestContext`) sends HTTP calls with shared `baseURL`, headers and cookies. Use it for pure API suites, or to seed/verify data in UI tests.

```ts
import { test, expect } from "@playwright/test";

test.use({
  baseURL: "http://localhost:8000",
  extraHTTPHeaders: { Authorization: `Bearer ${process.env.API_TOKEN}` },
});

test.describe("Items API", () => {
  let id: number;

  test("create item", async ({ request }) => {
    const res = await request.post("/items", { data: { name: "Pen", price: 150 } });
    expect(res.status()).toBe(201);
    expect(res.headers()["content-type"]).toContain("application/json");
    const body = await res.json();
    expect(body).toMatchObject({ name: "Pen", price: 150, id: expect.any(Number) });
    id = body.id;
  });

  test("validation error", async ({ request }) => {
    const res = await request.post("/items", { data: { name: "" } });
    expect(res.status()).toBe(422);
  });

  test("list with query params", async ({ request }) => {
    const res = await request.get("/items", { params: { limit: 5, q: "pen" } });
    await expect(res).toBeOK();                  // 2xx
    expect((await res.json()).length).toBeLessThanOrEqual(5);
  });
});

// Standalone context (e.g. in globalSetup)
import { request } from "@playwright/test";
const ctx = await request.newContext({ baseURL: "http://localhost:8000" });
await ctx.post("/test/reset"); await ctx.dispose();
```

Other options: `form` (urlencoded), `multipart` (file uploads), `failOnStatusCode`, `timeout`, `ignoreHTTPSErrors`.

> [!warning] Tests that depend on each other (shared `id` above) must run serially: `test.describe.configure({ mode: "serial" })`. Prefer each test creating its own data.

## Related
- [[API Testing Strategy]] · [[Supertest MOC]] · [[FastAPI Testing]] · [[HTTP Status Codes]]
