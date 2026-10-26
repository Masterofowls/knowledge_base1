---
tags: [jest, testing, snippet]
aliases: [beforeEach, afterAll, setupFiles]
created: 2026-10-26
status: evergreen
---
# Jest Setup and Teardown

```ts
let db: TestDb;

beforeAll(async () => { db = await TestDb.start(); });   // once per file
afterAll(async () => { await db.stop(); });

beforeEach(async () => { await db.truncateAll(); });     // before every test in scope
afterEach(() => { jest.restoreAllMocks(); });

describe("with admin", () => {
  let admin: User;
  beforeEach(async () => { admin = await db.createUser({ role: "admin" }); }); // nested: runs after outer beforeEach
  test("can delete", async () => { /* ... */ });
});
```

Order: outer `beforeEach` → inner `beforeEach` → test → inner `afterEach` → outer `afterEach`.

## Global setup (config)
| Option | Runs |
|---|---|
| `globalSetup` / `globalTeardown` | once per run, separate context (start Docker, migrate DB) |
| `setupFiles` | before framework loads, per test file (polyfills, env) |
| `setupFilesAfterEnv` | after framework loads, per file (`@testing-library/jest-dom`, custom matchers) |

> [!tip] Factory functions (`buildUser({ overrides })`) often beat deeply nested `beforeEach` — keeps each test's Arrange visible.

## Related
- [[pytest Fixtures]] · [[Playwright Fixtures]] · [[Test Isolation and Flakiness]] · [[Jest Configuration]]
