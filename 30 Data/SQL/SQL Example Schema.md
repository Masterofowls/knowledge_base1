---
tags: [sql, snippet]
aliases: [shop schema]
created: 2026-10-26
status: evergreen
---
# SQL Example Schema

Used throughout the SQL notes.

```sql
CREATE TABLE customers (
  id          BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  email       TEXT NOT NULL UNIQUE,
  name        TEXT NOT NULL,
  country     CHAR(2),
  created_at  TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE TABLE products (
  id     BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  sku    TEXT NOT NULL UNIQUE,
  name   TEXT NOT NULL,
  price  NUMERIC(10,2) NOT NULL CHECK (price >= 0),
  category TEXT
);

CREATE TABLE orders (
  id           BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  customer_id  BIGINT NOT NULL REFERENCES customers(id) ON DELETE CASCADE,
  status       TEXT NOT NULL DEFAULT 'pending' CHECK (status IN ('pending','paid','shipped','cancelled')),
  created_at   TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE TABLE order_items (
  order_id    BIGINT REFERENCES orders(id) ON DELETE CASCADE,
  product_id  BIGINT REFERENCES products(id),
  quantity    INT NOT NULL CHECK (quantity > 0),
  unit_price  NUMERIC(10,2) NOT NULL,
  PRIMARY KEY (order_id, product_id)
);
```

```
customers 1──* orders 1──* order_items *──1 products
```

> [!tip] Spin up Postgres to practise: `docker run --rm -e POSTGRES_PASSWORD=pw -p 5432:5432 postgres:17` then `psql -h localhost -U postgres`.

## Related
- [[SQL DDL and Constraints]] · [[SQL MOC]]
