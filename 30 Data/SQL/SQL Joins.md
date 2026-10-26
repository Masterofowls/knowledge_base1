---
tags: [sql, concept]
aliases: [JOIN, INNER JOIN, LEFT JOIN, anti-join]
created: 2026-10-26
status: evergreen
---
# SQL Joins

| Join | Returns |
|---|---|
| `INNER JOIN` | only matching rows from both |
| `LEFT JOIN` | all left rows + matches (NULLs when none) |
| `RIGHT JOIN` | mirror of LEFT (rarely used — swap tables) |
| `FULL OUTER JOIN` | all rows from both sides |
| `CROSS JOIN` | Cartesian product |
| self join | table joined to itself (hierarchies, comparisons) |

```sql
-- Orders with customer names
SELECT o.id, c.name, o.status
FROM orders o
JOIN customers c ON c.id = o.customer_id;

-- Customers and their order count, including those with zero
SELECT c.name, COUNT(o.id) AS orders
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.id
GROUP BY c.id, c.name;

-- Anti-join: customers who never ordered
SELECT c.* FROM customers c
LEFT JOIN orders o ON o.customer_id = c.id
WHERE o.id IS NULL;
-- equivalently: WHERE NOT EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = c.id)

-- Semi-join: customers who ordered at least once (no duplicates)
SELECT * FROM customers c WHERE EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = c.id);
```

> [!warning] LEFT JOIN + WHERE on the right table turns it into an INNER JOIN
> `LEFT JOIN orders o ON ... WHERE o.status = 'paid'` drops customers without paid orders. Put the condition in the `ON` clause to keep them.

> [!warning] Joining two one-to-many relations at once multiplies rows (fan-out) — aggregates double-count. Aggregate in subqueries first.

## Related
- [[SQL Aggregation and GROUP BY]] · [[SQL Indexes]] (index FK columns!)
- [[Django QuerySets and Performance]] — `select_related` = JOIN
