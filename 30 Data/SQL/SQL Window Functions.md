---
tags: [sql, concept]
aliases: [OVER, PARTITION BY, ROW_NUMBER, RANK, LAG, running total]
created: 2026-10-26
status: evergreen
---
# SQL Window Functions

> [!summary] TL;DR
> `fn() OVER (PARTITION BY … ORDER BY … frame)` computes a value **across related rows without collapsing them** (unlike GROUP BY).

```sql
SELECT customer_id, id AS order_id, created_at,
       ROW_NUMBER() OVER w                                     AS nth_order,
       LAG(created_at) OVER w                                  AS prev_order_at,
       created_at - LAG(created_at) OVER w                     AS gap,
       COUNT(*) OVER (PARTITION BY customer_id)                AS customer_total
FROM orders
WINDOW w AS (PARTITION BY customer_id ORDER BY created_at);
```

## Top-N per group (classic interview question)
```sql
WITH ranked AS (
  SELECT p.*, ROW_NUMBER() OVER (PARTITION BY category ORDER BY price DESC) AS rn
  FROM products p
)
SELECT * FROM ranked WHERE rn <= 3;
```

## Running total & moving average
```sql
SELECT day, revenue,
       SUM(revenue) OVER (ORDER BY day)                                   AS running_total,
       AVG(revenue) OVER (ORDER BY day ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS ma_7d
FROM daily_revenue;
```

| Function | Purpose |
|---|---|
| `ROW_NUMBER()` | 1,2,3,4 (unique) |
| `RANK()` | 1,2,2,4 (gaps on ties) |
| `DENSE_RANK()` | 1,2,2,3 |
| `NTILE(n)` | bucket into n groups |
| `LAG/LEAD(col, n)` | previous/next row value |
| `FIRST_VALUE/LAST_VALUE` | edge of frame (mind the default frame!) |
| `SUM/AVG/COUNT … OVER` | running/partitioned aggregates |

> [!warning] Default frame with `ORDER BY` is `RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW` — `LAST_VALUE` then returns the current row. Specify `ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING`.

## Related
- [[SQL Aggregation and GROUP BY]] · [[SQL Subqueries and CTEs]]
