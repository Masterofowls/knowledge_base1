---
tags: [sql, concept]
aliases: [query execution order, order of operations]
created: 2026-10-26
status: evergreen
---
# SQL Logical Query Order

> [!summary] TL;DR
> SQL is **written** `SELECT … FROM … WHERE …` but **evaluated** in a different logical order. This explains most "why can't I use that alias here?" errors.

| # | Clause | Does |
|---|---|---|
| 1 | `FROM` / `JOIN` | build the row set |
| 2 | `WHERE` | filter rows |
| 3 | `GROUP BY` | form groups |
| 4 | `HAVING` | filter groups |
| 5 | `SELECT` | compute columns, aliases, **window functions** |
| 6 | `DISTINCT` | dedupe |
| 7 | `ORDER BY` | sort (aliases OK here) |
| 8 | `LIMIT` / `OFFSET` | trim |

Consequences:
- Can't use a `SELECT` alias in `WHERE` → repeat the expression or use a [[SQL Subqueries and CTEs|CTE]].
- Can't filter on aggregates in `WHERE` → use `HAVING`.
- Can't filter on a window function in `WHERE` → wrap in a subquery/CTE (or `QUALIFY` in some DBs).

## Related
- [[SQL SELECT Basics]] · [[SQL Aggregation and GROUP BY]] · [[SQL Window Functions]]
