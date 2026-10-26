---
tags: [sql, concept]
aliases: [index, B-tree, EXPLAIN, composite index, query plan]
created: 2026-10-26
status: evergreen
---
# SQL Indexes

> [!summary] TL;DR
> An index is a sorted side-structure (usually a **B-tree**) that lets the DB find rows without scanning the whole table. Speeds reads, costs writes and storage.

```sql
CREATE INDEX idx_orders_customer ON orders (customer_id);              -- index FKs!
CREATE INDEX idx_orders_status_created ON orders (status, created_at DESC);  -- composite
CREATE UNIQUE INDEX idx_customers_email_lower ON customers (LOWER(email));  -- expression
CREATE INDEX idx_orders_pending ON orders (created_at) WHERE status = 'pending'; -- partial
CREATE INDEX CONCURRENTLY ...;      -- Postgres: no table lock (prod)
```

## Composite index — leftmost prefix rule
Index on `(status, created_at)` helps:
- `WHERE status = ?` ✅
- `WHERE status = ? AND created_at > ?` ✅
- `WHERE status = ? ORDER BY created_at` ✅
- `WHERE created_at > ?` ❌ (can't skip the first column, mostly)

Put **equality** columns first, then **range/sort** columns.

## Index killers
- Function on the column: `WHERE LOWER(email) = ?` (unless expression index)
- Leading wildcard: `LIKE '%abc'` (use trigram/full-text index)
- Implicit casts: comparing text column to a number
- Low selectivity (e.g. boolean) — planner may prefer a seq scan anyway

## Reading plans
```sql
EXPLAIN ANALYZE SELECT * FROM orders WHERE customer_id = 42 ORDER BY created_at DESC LIMIT 10;
```
Look for: `Seq Scan` on big tables, row-estimate vs actual mismatch, `Sort` with disk spill, nested loops over large sets.

> [!tip] **Covering index**: `CREATE INDEX ... (customer_id) INCLUDE (status)` lets an Index Only Scan answer without touching the table.

## Related
- [[SQL Joins]] · [[Django QuerySets and Performance]]
