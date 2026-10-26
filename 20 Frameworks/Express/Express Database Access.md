---
tags: [express, sql, snippet]
aliases: [prisma, node-postgres, pg pool, drizzle]
created: 2026-10-26
status: evergreen
---
# Express Database Access

## Option A — node-postgres (raw SQL, parameterized)
```ts
import pg from "pg";
export const pool = new pg.Pool({ connectionString: process.env.DATABASE_URL, max: 10 });

export async function getUser(id: number) {
  const { rows } = await pool.query("SELECT id, email, name FROM users WHERE id = $1", [id]);
  return rows[0] ?? null;
}

export async function transfer(from: number, to: number, amount: number) {
  const client = await pool.connect();
  try {
    await client.query("BEGIN");
    await client.query("UPDATE accounts SET balance = balance - $1 WHERE id = $2", [amount, from]);
    await client.query("UPDATE accounts SET balance = balance + $1 WHERE id = $2", [amount, to]);
    await client.query("COMMIT");
  } catch (e) {
    await client.query("ROLLBACK");
    throw e;
  } finally {
    client.release();                 // always return the connection to the pool
  }
}
```

## Option B — Prisma (schema-first ORM, typed client)
```prisma
// prisma/schema.prisma
model User {
  id     Int     @id @default(autoincrement())
  email  String  @unique
  name   String
  orders Order[]
}
model Order {
  id     Int  @id @default(autoincrement())
  total  Int
  user   User @relation(fields: [userId], references: [id], onDelete: Cascade)
  userId Int
  @@index([userId])
}
```
```bash
npx prisma migrate dev --name init     # create & apply migration, generate client
npx prisma studio                      # GUI
```
```ts
import { PrismaClient } from "@prisma/client";
export const prisma = new PrismaClient();

const user = await prisma.user.findUnique({ where: { id }, include: { orders: true } });
await prisma.$transaction([ /* ... */ ]);
```

Other choices: **Drizzle** (SQL-like, lightweight TS), Kysely (type-safe query builder), TypeORM / Sequelize (older ORMs).

> [!warning] Never interpolate user input into SQL → [[SQL Injection and Parameterized Queries]]. Index every foreign key → [[SQL Indexes]].

## Related
- [[SQL MOC]] · [[SQL Transactions and Isolation]] · [[Supertest with a Database]] · [[FastAPI Database with SQLAlchemy]]
