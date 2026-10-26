---
tags: [sql, concept]
aliases: [SELECT, ORDER BY, LIMIT, DISTINCT]
created: 2026-10-26
status: evergreen
---
# SQL SELECT Basics

```sql
SELECT id,
       name,
       price * 1.2            AS price_with_vat,
       UPPER(category)        AS category
FROM products
WHERE price > 10
ORDER BY price DESC, name ASC
LIMIT 10 OFFSET 20;           -- page 3 of 10 (SQL standard: OFFSET 20 ROWS FETCH NEXT 10 ROWS ONLY)

SELECT DISTINCT country FROM customers;
SELECT COUNT(DISTINCT country) FROM customers;

SELECT name,
       CASE WHEN price < 10 THEN 'cheap'
            WHEN price < 100 THEN 'mid'
            ELSE 'premium' END AS tier
FROM products;
```

> [!warning] `SELECT *` in application code breaks when columns change and fetches more than needed. Name columns.

> [!tip] Keyset pagination beats OFFSET on large tables:
> ```sql
> SELECT * FROM orders WHERE id < :last_seen_id ORDER BY id DESC LIMIT 20;
> ```

## Related
- [[SQL Logical Query Order]] · [[SQL Filtering and NULL]]
