---
tags: [supertest, testing, sql, concept]
aliases: [integration test database node]
created: 2026-10-26
status: growing
---
# Supertest with a Database

Strategies for real-DB integration tests (fastest → most realistic):

| Strategy | How | Trade-off |
|---|---|---|
| Transaction rollback | begin tx in `beforeEach`, rollback in `afterEach` (app must use the same connection) | fastest; tricky with pools |
| Truncate tables | `TRUNCATE ... RESTART IDENTITY CASCADE` in `beforeEach` | simple; slower with many tables |
| DB per worker | `test_db_${process.env.JEST_WORKER_ID}` | parallel-safe |
| Testcontainers | throwaway Postgres in Docker per run | realistic, slower startup |

```ts
// tests/setup-db.ts
import { GenericContainer } from "testcontainers";
let container;
export default async function globalSetup() {
  container = await new GenericContainer("postgres:17")
    .withEnvironment({ POSTGRES_PASSWORD: "pw" })
    .withExposedPorts(5432)
    .start();
  process.env.DATABASE_URL = `postgres://postgres:pw@${container.getHost()}:${container.getMappedPort(5432)}/postgres`;
  await runMigrations(process.env.DATABASE_URL);
}
```

```ts
beforeEach(async () => { await db.query("TRUNCATE users, orders RESTART IDENTITY CASCADE"); });
afterAll(async () => { await db.end(); });   // avoid "Jest did not exit" open handles
```

## Related
- [[Test Isolation and Flakiness]] · [[SQL Transactions and Isolation]] · [[pytest Fixtures]] (same rollback pattern)
