---
tags: [sql, concept]
aliases: [ACID, transaction, isolation levels, BEGIN COMMIT ROLLBACK, locking]
created: 2026-10-26
status: evergreen
---
# SQL Transactions and Isolation

> [!summary] TL;DR
> A transaction groups statements into an all-or-nothing unit (**ACID**: Atomic, Consistent, Isolated, Durable). Isolation level controls which concurrency anomalies you can see.

```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;            -- or ROLLBACK;

SAVEPOINT before_risky;
-- ...
ROLLBACK TO SAVEPOINT before_risky;
```

## Isolation levels vs anomalies
| Level | Dirty read | Non-repeatable read | Phantom | Notes |
|---|---|---|---|---|
| Read Uncommitted | possible | possible | possible | Postgres treats as RC |
| **Read Committed** | ❌ | possible | possible | Postgres default |
| Repeatable Read | ❌ | ❌ | possible (❌ in PG) | MySQL InnoDB default |
| Serializable | ❌ | ❌ | ❌ | may abort → retry |

## Lost update & how to prevent it
```sql
-- ❌ read-modify-write in app code races
-- ✅ atomic update
UPDATE products SET stock = stock - 1 WHERE id = 7 AND stock > 0;
-- ✅ pessimistic lock
SELECT * FROM products WHERE id = 7 FOR UPDATE;
-- ✅ optimistic lock (version column)
UPDATE products SET stock = 4, version = version + 1 WHERE id = 7 AND version = 12;
-- job queues
SELECT * FROM jobs WHERE status = 'new' LIMIT 10 FOR UPDATE SKIP LOCKED;
```

> [!warning] Long transactions hold locks and bloat MVCC — keep them short, never wait for user input or HTTP calls inside one.

## In frameworks
- Django: `with transaction.atomic(): ...`, `select_for_update()`
- SQLAlchemy: `async with session.begin(): ...`
- See [[Python Context Managers]]

## Related
- [[SQL DML and Upsert]] · [[Django Models and ORM]] · [[FastAPI Database with SQLAlchemy]]
