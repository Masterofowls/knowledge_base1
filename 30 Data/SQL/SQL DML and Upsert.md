---
tags: [sql, snippet]
aliases: [INSERT, UPDATE, DELETE, upsert, ON CONFLICT, RETURNING]
created: 2026-10-26
status: evergreen
---
# SQL DML and Upsert

```sql
INSERT INTO customers (email, name, country)
VALUES ('ada@example.com', 'Ada', 'GB'),
       ('alan@example.com', 'Alan', 'GB')
RETURNING id;                                   -- Postgres/SQLite

INSERT INTO archive_orders SELECT * FROM orders WHERE created_at < '2025-01-01';

UPDATE products SET price = price * 0.9 WHERE category = 'sale' RETURNING id, price;

-- UPDATE with join (Postgres)
UPDATE orders o SET status = 'cancelled'
FROM customers c
WHERE c.id = o.customer_id AND c.email LIKE '%@spam.test';

DELETE FROM orders WHERE status = 'cancelled' AND created_at < now() - INTERVAL '1 year';
```

## Upsert
```sql
-- Postgres / SQLite
INSERT INTO products (sku, name, price) VALUES ('PEN-1', 'Pen', 1.50)
ON CONFLICT (sku) DO UPDATE SET name = EXCLUDED.name, price = EXCLUDED.price;

INSERT ... ON CONFLICT DO NOTHING;

-- MySQL
INSERT INTO products (sku, name, price) VALUES ('PEN-1', 'Pen', 1.50)
ON DUPLICATE KEY UPDATE name = VALUES(name), price = VALUES(price);

-- Standard SQL
MERGE INTO products t USING staging s ON t.sku = s.sku
WHEN MATCHED THEN UPDATE SET price = s.price
WHEN NOT MATCHED THEN INSERT (sku, name, price) VALUES (s.sku, s.name, s.price);
```

> [!warning] Always run `UPDATE`/`DELETE` as a `SELECT` with the same `WHERE` first, inside a transaction you can roll back. See [[SQL Transactions and Isolation]].

## Related
- [[SQL DDL and Constraints]] · [[SQL Injection and Parameterized Queries]]
- [[HTTP Methods]] — PUT ≈ upsert semantics
