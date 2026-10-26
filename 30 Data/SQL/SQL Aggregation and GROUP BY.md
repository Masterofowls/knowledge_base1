---
tags: [sql, concept]
aliases: [GROUP BY, HAVING, aggregate functions, COUNT SUM AVG]
created: 2026-10-26
status: evergreen
---
# SQL Aggregation and GROUP BY

```sql
-- Revenue per category, only categories over 1000
SELECT p.category,
       COUNT(DISTINCT oi.order_id)        AS orders,
       SUM(oi.quantity * oi.unit_price)   AS revenue,
       ROUND(AVG(oi.unit_price), 2)       AS avg_price
FROM order_items oi
JOIN products p ON p.id = oi.product_id
GROUP BY p.category
HAVING SUM(oi.quantity * oi.unit_price) > 1000
ORDER BY revenue DESC;

-- Conditional aggregation (pivot-ish)
SELECT DATE_TRUNC('month', created_at) AS month,
       COUNT(*) FILTER (WHERE status = 'paid')       AS paid,
       COUNT(*) FILTER (WHERE status = 'cancelled')  AS cancelled
       -- portable: SUM(CASE WHEN status = 'paid' THEN 1 ELSE 0 END)
FROM orders
GROUP BY 1
ORDER BY 1;

SELECT customer_id, STRING_AGG(status, ',') FROM orders GROUP BY customer_id;  -- MySQL: GROUP_CONCAT
```

Rules:
- Every non-aggregated column in `SELECT` must be in `GROUP BY` (or functionally dependent on the PK).
- `WHERE` filters rows *before* grouping; `HAVING` filters groups *after*.
- Aggregates ignore NULLs (except `COUNT(*)`).

Extras: `GROUPING SETS`, `ROLLUP`, `CUBE` for subtotals.

## Related
- [[SQL Window Functions]] — aggregate **without** collapsing rows
- [[SQL Logical Query Order]] · [[JS Array Methods]]
