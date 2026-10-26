---
tags: [jest, testing, snippet]
aliases: [async tests jest, resolves rejects]
created: 2026-10-26
status: evergreen
---
# Jest Async Testing

```ts
test("fetches user", async () => {
  const user = await api.getUser(1);
  expect(user.name).toBe("Ada");
});

test("resolves / rejects", async () => {
  await expect(api.getUser(1)).resolves.toMatchObject({ id: 1 });
  await expect(api.getUser(999)).rejects.toThrow("Not found");   // ⚠️ must await/return
});

test("callback style", (done) => {
  emitter.once("ready", (payload) => {
    try { expect(payload.ok).toBe(true); done(); } catch (e) { done(e); }
  });
});

test("asserts errors are thrown", async () => {
  expect.assertions(1);                 // guard: fail if catch never runs
  try { await risky(); } catch (e) { expect(e).toBeInstanceOf(TimeoutError); }
});
```

> [!warning] Forgetting `await`/`return` on a promise assertion makes the test pass **before** the promise settles — a silent false positive. Lint with `eslint-plugin-jest` (`valid-expect`, `no-floating-promises`).

Timeout per test: `test("slow", fn, 10_000)` or `jest.setTimeout(10_000)`.

## Related
- [[JS Promises and Async-Await]] · [[Jest Fake Timers]] · [[pytest Async Testing]]
