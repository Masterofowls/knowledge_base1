---
tags: [sql, concept]
aliases: [WHERE, NULL, three-valued logic, LIKE, IN, BETWEEN]
created: 2026-10-26
status: evergreen
---
# SQL Filtering and NULL

```sql
WHERE status IN ('paid', 'shipped')
  AND created_at >= '2026-01-01' AND created_at < '2026-02-01'   -- half-open ranges for time
  AND name ILIKE '%pen%'          -- Postgres case-insensitive; LIKE is case-sensitive
  AND price BETWEEN 10 AND 20     -- inclusive both ends
  AND NOT EXISTS (SELECT 1 FROM refunds r WHERE r.order_id = o.id)
```

## NULL = "unknown" → three-valued logic
- `NULL = NULL` → **NULL** (not true!). Use `IS NULL` / `IS NOT NULL`.
- `WHERE` keeps only rows where the condition is **TRUE** (NULL rows dropped).
- `COUNT(col)` skips NULLs; `COUNT(*)` doesn't.
- `NULL + 1` → NULL.

```sql
SELECT COALESCE(country, 'unknown') FROM customers;     -- first non-null
SELECT NULLIF(qty, 0);                                  -- NULL if qty = 0 (avoid /0)
WHERE a IS DISTINCT FROM b                              -- null-safe comparison
```

> [!warning] The `NOT IN` trap
> `WHERE id NOT IN (SELECT customer_id FROM orders)` returns **no rows** if any `customer_id` is NULL. Use `NOT EXISTS`.

## Related
- [[SQL SELECT Basics]] · [[JS Data Types and Coercion]] — null vs undefined analogy
