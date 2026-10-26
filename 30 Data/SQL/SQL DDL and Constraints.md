---
tags: [sql, concept]
aliases: [CREATE TABLE, ALTER TABLE, primary key, foreign key, constraints]
created: 2026-10-26
status: evergreen
---
# SQL DDL and Constraints

```sql
CREATE TABLE IF NOT EXISTS reviews (
  id          BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  product_id  BIGINT NOT NULL REFERENCES products(id) ON DELETE CASCADE,
  customer_id BIGINT NOT NULL REFERENCES customers(id),
  rating      SMALLINT NOT NULL CHECK (rating BETWEEN 1 AND 5),
  body        TEXT,
  created_at  TIMESTAMPTZ NOT NULL DEFAULT now(),
  UNIQUE (product_id, customer_id)          -- one review per customer per product
);

ALTER TABLE products ADD COLUMN stock INT NOT NULL DEFAULT 0;
ALTER TABLE products ALTER COLUMN name TYPE VARCHAR(200);
ALTER TABLE products ADD CONSTRAINT price_positive CHECK (price > 0);
ALTER TABLE products DROP COLUMN category;
ALTER TABLE products RENAME COLUMN name TO title;
DROP TABLE IF EXISTS reviews;
TRUNCATE orders RESTART IDENTITY CASCADE;

CREATE VIEW paid_orders AS SELECT * FROM orders WHERE status = 'paid';
```

## Constraint cheat sheet
| Constraint | Guarantees |
|---|---|
| `PRIMARY KEY` | unique + not null; row identity |
| `FOREIGN KEY … REFERENCES` | referential integrity |
| `UNIQUE` | no duplicates (NULLs usually allowed multiple times) |
| `NOT NULL` | value required |
| `CHECK (expr)` | arbitrary rule |
| `DEFAULT` | value when omitted |

FK actions: `CASCADE`, `SET NULL`, `SET DEFAULT`, `RESTRICT`, `NO ACTION`.

> [!tip] Put integrity rules in the database, not only in app code — the DB is the last line of defence against bugs and concurrent writes.

## Types to know (Postgres)
`BIGINT`, `NUMERIC(p,s)` for money (never FLOAT), `TEXT`, `BOOLEAN`, `TIMESTAMPTZ` (always TZ-aware), `DATE`, `UUID`, `JSONB`, arrays, `ENUM`.

## Related
- [[SQL Example Schema]] · [[SQL Normalization]] · [[SQL Indexes]]
- [[Django Migrations]] · [[FastAPI Database with SQLAlchemy]] (Alembic)
