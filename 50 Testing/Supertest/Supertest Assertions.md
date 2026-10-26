---
tags: [supertest, testing, snippet]
aliases: [supertest expect]
created: 2026-10-26
status: evergreen
---
# Supertest Assertions

Supertest's own `.expect()` (chainable) vs Jest's `expect()` on the response — both are fine; Jest assertions give better diffs.

```ts
await request(app)
  .get("/users/1")
  .expect(200)                                   // status
  .expect("Content-Type", /json/)                // header (string or regex)
  .expect({ id: 1, name: "Ada" })                // exact body (deep equal)
  .expect((res) => {                             // custom function — throw to fail
    if (!res.body.email.includes("@")) throw new Error("bad email");
  });

// Combined with Jest for richer checks
const res = await request(app).get("/users?limit=2").expect(200);
expect(res.body).toHaveLength(2);
expect(res.body[0]).toEqual(expect.objectContaining({ id: expect.any(Number) }));
expect(res.headers["x-total-count"]).toBe("10");

// Errors
const bad = await request(app).post("/users").send({}).expect(400);
expect(bad.body.detail).toMatch(/email/);

await request(app).get("/users/999").expect(404);
await request(app).delete("/users/1").expect(204).expect((r) => expect(r.text).toBe(""));
```

Response object: `res.status`, `res.body` (parsed JSON), `res.text` (raw), `res.headers`, `res.type`, `res.redirect`.

> [!warning] Always `await` (or `return`) the request — otherwise Jest finishes before the assertions run. See [[Jest Async Testing]].

## Related
- [[Jest Matchers]] · [[HTTP Status Codes]] · [[HTTP Headers]]
