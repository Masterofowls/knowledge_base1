---
tags: [sql, guide]
aliases: [Learn SQL, SQL roadmap]
created: 2026-10-26
---
# SQL Getting Started

## 1. Get a database
| Option | Command |
|---|---|
| SQLite (no server) | `winget install SQLite.SQLite` / `brew install sqlite` → `sqlite3 test.db` |
| PostgreSQL in Docker | `docker run -d --name pg -e POSTGRES_PASSWORD=pw -p 5432:5432 postgres:17` |
| PostgreSQL native | `winget install PostgreSQL.PostgreSQL.17` |

GUI clients: **DBeaver** (free, all DBs), pgAdmin, TablePlus, VS Code "SQLTools".

## 2. Hello SQL
```sql
CREATE TABLE notes (id INTEGER PRIMARY KEY, title TEXT NOT NULL, created_at TEXT DEFAULT CURRENT_TIMESTAMP);
INSERT INTO notes (title) VALUES ('Learn SELECT'), ('Learn JOIN');
SELECT * FROM notes WHERE title LIKE '%JOIN%';
```
psql essentials: `\l` databases · `\c db` connect · `\dt` tables · `\d table` describe · `\x` expanded output · `\q` quit.

## 3. Learning checklist
- [ ] SELECT, ORDER BY, LIMIT → [[SQL SELECT Basics]]
- [ ] WHERE & NULL → [[SQL Filtering and NULL]]
- [ ] Logical query order → [[SQL Logical Query Order]]
- [ ] Joins → [[SQL Joins]]
- [ ] GROUP BY / HAVING → [[SQL Aggregation and GROUP BY]]
- [ ] Subqueries & CTEs → [[SQL Subqueries and CTEs]]
- [ ] Window functions → [[SQL Window Functions]]
- [ ] DDL & constraints → [[SQL DDL and Constraints]]
- [ ] INSERT/UPDATE/DELETE/upsert → [[SQL DML and Upsert]]
- [ ] Normalization → [[SQL Normalization]]
- [ ] Indexes & EXPLAIN → [[SQL Indexes]]
- [ ] Transactions → [[SQL Transactions and Isolation]]
- [ ] Injection & parameters → [[SQL Injection and Parameterized Queries]]
- [ ] Practise: [[Tutorial - SQL Practice on the Shop Database]]

## 4. Beginner mistakes
- `= NULL` instead of `IS NULL`.
- `COUNT(*)` with LEFT JOIN counting the NULL row as 1 (use `COUNT(right.id)`).
- `UPDATE`/`DELETE` without `WHERE` — always run the `SELECT` first, inside a transaction.
- Storing money in `FLOAT`.
- Building SQL strings with user input.

## Resources
- SQLBolt — sqlbolt.com (interactive basics)
- PostgreSQL tutorial — postgresqltutorial.com
- "Use The Index, Luke" — use-the-index-luke.com
- pgexercises.com — practice problems
