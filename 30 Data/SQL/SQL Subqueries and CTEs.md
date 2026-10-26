---
tags: [sql, concept]
aliases: [CTE, WITH, subquery, recursive CTE]
created: 2026-10-26
status: evergreen
---
# SQL Subqueries and CTEs

```sql
-- Scalar subquery
SELECT name, price, price - (SELECT AVG(price) FROM products) AS diff_from_avg FROM products;

-- Correlated subquery (runs per outer row, logically)
SELECT c.name,
       (SELECT MAX(o.created_at) FROM orders o WHERE o.customer_id = c.id) AS last_order
FROM customers c;

-- Derived table
SELECT * FROM (SELECT customer_id, COUNT(*) n FROM orders GROUP BY customer_id) t WHERE n > 5;
```

## CTEs — named, readable steps
```sql
WITH order_totals AS (
  SELECT order_id, SUM(quantity * unit_price) AS total
  FROM order_items GROUP BY order_id
),
big_orders AS (
  SELECT * FROM order_totals WHERE total > 500
)
SELECT c.name, b.total
FROM big_orders b
JOIN orders o    ON o.id = b.order_id
JOIN customers c ON c.id = o.customer_id;
```

## Recursive CTE — trees & graphs
```sql
WITH RECURSIVE subordinates AS (
  SELECT id, manager_id, name, 1 AS depth FROM employees WHERE id = 1
  UNION ALL
  SELECT e.id, e.manager_id, e.name, s.depth + 1
  FROM employees e JOIN subordinates s ON e.manager_id = s.id
)
SELECT * FROM subordinates;

-- generate a date series (Postgres: generate_series)
WITH RECURSIVE d(day) AS (SELECT DATE '2026-01-01' UNION ALL SELECT day + 1 FROM d WHERE day < '2026-01-31')
SELECT day FROM d;
```

## Related
- [[SQL Window Functions]] · [[SQL Logical Query Order]]
