---
tags: [tutorial, sql]
aliases: [SQL exercises, SQL practice]
created: 2026-10-26
level: beginner-intermediate
time: 2-3h
---
# Tutorial - SQL Practice on the Shop Database

> [!abstract] What you'll practise
> 20 graded exercises on the [[SQL Example Schema|shop schema]]: filtering, joins, aggregation, subqueries, CTEs, window functions and data modification. Try each before opening the solution.

## Setup (pick one)
**PostgreSQL in Docker (recommended):**
```bash
docker run -d --name shopdb -e POSTGRES_PASSWORD=pw -p 5432:5432 postgres:17
docker exec -it shopdb psql -U postgres
```
**SQLite (zero install):** `sqlite3 shop.db` — replace `GENERATED ALWAYS AS IDENTITY` with `INTEGER PRIMARY KEY`, `TIMESTAMPTZ` with `TEXT`, and `now()` with `CURRENT_TIMESTAMP`.

Create the tables from [[SQL Example Schema]], then load sample data:
```sql
INSERT INTO customers (email, name, country) VALUES
 ('ada@example.com','Ada','GB'), ('alan@example.com','Alan','GB'), ('grace@example.com','Grace','US'),
 ('linus@example.com','Linus','FI'), ('margaret@example.com','Margaret','US'), ('ken@example.com','Ken', NULL);

INSERT INTO products (sku, name, price, category) VALUES
 ('PEN-1','Pen',1.50,'stationery'), ('NB-1','Notebook',4.00,'stationery'), ('MUG-1','Mug',8.50,'kitchen'),
 ('KB-1','Keyboard',49.00,'electronics'), ('MS-1','Mouse',19.00,'electronics'), ('LAMP-1','Desk lamp',25.00,'home');

INSERT INTO orders (customer_id, status, created_at) VALUES
 (1,'paid','2026-09-01'), (1,'shipped','2026-09-15'), (2,'paid','2026-09-20'), (3,'cancelled','2026-10-01'),
 (3,'paid','2026-10-02'), (4,'pending','2026-10-10'), (5,'paid','2026-10-12'), (1,'paid','2026-10-20');

INSERT INTO order_items (order_id, product_id, quantity, unit_price) VALUES
 (1,1,10,1.50), (1,2,2,4.00), (2,4,1,49.00), (3,3,2,8.50), (4,5,1,19.00), (5,4,1,49.00), (5,5,1,19.00),
 (6,6,1,25.00), (7,2,5,4.00), (7,1,3,1.50), (8,3,1,8.50), (8,6,2,25.00);
```

## 🟢 Level 1 — SELECT & filtering ([[SQL SELECT Basics]], [[SQL Filtering and NULL]])
1. All products under 10, cheapest first.
2. Customers without a country.
3. Orders created in October 2026 that are not cancelled.
4. Product names containing "e" (case-insensitive).

> [!success]- Solutions 1–4
> ```sql
> SELECT name, price FROM products WHERE price < 10 ORDER BY price;
> SELECT * FROM customers WHERE country IS NULL;            -- not "= NULL"!
> SELECT * FROM orders WHERE created_at >= '2026-10-01' AND created_at < '2026-11-01' AND status <> 'cancelled';
> SELECT name FROM products WHERE name ILIKE '%e%';        -- SQLite: LIKE is case-insensitive for ASCII
> ```

## 🟡 Level 2 — Joins ([[SQL Joins]])
5. Each order with customer name and status.
6. Every customer and how many orders they have — **including zero**.
7. Customers who have never ordered.
8. Products that were never sold.

> [!success]- Solutions 5–8
> ```sql
> SELECT o.id, c.name, o.status FROM orders o JOIN customers c ON c.id = o.customer_id;
>
> SELECT c.name, COUNT(o.id) AS orders
> FROM customers c LEFT JOIN orders o ON o.customer_id = c.id
> GROUP BY c.id, c.name ORDER BY orders DESC;               -- COUNT(o.id), not COUNT(*)!
>
> SELECT c.* FROM customers c WHERE NOT EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = c.id);
>
> SELECT p.* FROM products p LEFT JOIN order_items oi ON oi.product_id = p.id WHERE oi.order_id IS NULL;
> ```
> Q8 returns nothing with this data — add a product and re-run. Testing your query against an edge case is good practice.

## 🟡 Level 3 — Aggregation ([[SQL Aggregation and GROUP BY]])
9. Total of each order (sum of quantity × unit_price).
10. Revenue per category for paid + shipped orders only.
11. Customers who spent more than 50 in total (non-cancelled orders).
12. Number of orders per status per month.

> [!success]- Solutions 9–12
> ```sql
> SELECT order_id, SUM(quantity * unit_price) AS total FROM order_items GROUP BY order_id ORDER BY order_id;
>
> SELECT p.category, SUM(oi.quantity * oi.unit_price) AS revenue
> FROM order_items oi JOIN orders o ON o.id = oi.order_id JOIN products p ON p.id = oi.product_id
> WHERE o.status IN ('paid','shipped')
> GROUP BY p.category ORDER BY revenue DESC;
>
> SELECT c.name, SUM(oi.quantity * oi.unit_price) AS spent
> FROM customers c JOIN orders o ON o.customer_id = c.id JOIN order_items oi ON oi.order_id = o.id
> WHERE o.status <> 'cancelled'
> GROUP BY c.id, c.name HAVING SUM(oi.quantity * oi.unit_price) > 50;
>
> SELECT DATE_TRUNC('month', created_at) AS month, status, COUNT(*)      -- SQLite: strftime('%Y-%m', created_at)
> FROM orders GROUP BY 1, 2 ORDER BY 1, 2;
> ```

## 🟠 Level 4 — Subqueries, CTEs, windows ([[SQL Subqueries and CTEs]], [[SQL Window Functions]])
13. Products priced above the average product price.
14. Using a CTE: the largest order (by total) and its customer.
15. Each customer's orders numbered 1, 2, 3… by date.
16. Days between each customer's consecutive orders.
17. Most expensive product in each category.
18. Running total of revenue by order date.

> [!success]- Solutions 13–18
> ```sql
> SELECT name, price FROM products WHERE price > (SELECT AVG(price) FROM products);
>
> WITH totals AS (
>   SELECT order_id, SUM(quantity * unit_price) AS total FROM order_items GROUP BY order_id
> )
> SELECT c.name, t.order_id, t.total
> FROM totals t JOIN orders o ON o.id = t.order_id JOIN customers c ON c.id = o.customer_id
> ORDER BY t.total DESC LIMIT 1;
>
> SELECT customer_id, id, created_at,
>        ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY created_at) AS nth
> FROM orders;
>
> SELECT customer_id, created_at,
>        created_at - LAG(created_at) OVER (PARTITION BY customer_id ORDER BY created_at) AS gap
> FROM orders;
>
> WITH ranked AS (
>   SELECT p.*, RANK() OVER (PARTITION BY category ORDER BY price DESC) AS r FROM products p
> )
> SELECT category, name, price FROM ranked WHERE r = 1;
>
> WITH daily AS (
>   SELECT o.created_at::date AS day, SUM(oi.quantity * oi.unit_price) AS revenue
>   FROM orders o JOIN order_items oi ON oi.order_id = o.id
>   WHERE o.status <> 'cancelled' GROUP BY 1
> )
> SELECT day, revenue, SUM(revenue) OVER (ORDER BY day) AS running_total FROM daily ORDER BY day;
> ```

## 🔴 Level 5 — Changing data safely ([[SQL DML and Upsert]], [[SQL Transactions and Isolation]])
19. In a transaction: raise all electronics prices by 10%, check the result, then roll back.
20. Upsert a product by SKU: insert `PEN-1` with a new price — update it if it exists.

> [!success]- Solutions 19–20
> ```sql
> BEGIN;
> UPDATE products SET price = ROUND(price * 1.10, 2) WHERE category = 'electronics' RETURNING sku, price;
> SELECT sku, price FROM products WHERE category = 'electronics';
> ROLLBACK;
>
> INSERT INTO products (sku, name, price, category) VALUES ('PEN-1', 'Pen', 1.75, 'stationery')
> ON CONFLICT (sku) DO UPDATE SET price = EXCLUDED.price
> RETURNING *;
> ```

## Bonus — read a query plan
```sql
EXPLAIN ANALYZE SELECT * FROM orders WHERE customer_id = 1;
CREATE INDEX idx_orders_customer ON orders (customer_id);
EXPLAIN ANALYZE SELECT * FROM orders WHERE customer_id = 1;
```
With 8 rows Postgres will still pick a seq scan — insert 100 000 rows with `generate_series` and compare ([[SQL Indexes]]).

## Related
- [[Tutorials MOC]] · [[SQL MOC]] · [[SQL Logical Query Order]]
