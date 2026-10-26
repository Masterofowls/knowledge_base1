---
tags: [sql, concept]
aliases: [normal forms, 1NF 2NF 3NF, denormalization]
created: 2026-10-26
status: evergreen
---
# SQL Normalization

> [!summary] TL;DR
> Normalize to remove redundancy and update anomalies: **every non-key column depends on the key, the whole key, and nothing but the key.** Denormalize deliberately for read performance.

| Form | Rule | Violation example |
|---|---|---|
| **1NF** | atomic values, no repeating groups | `tags = 'a,b,c'` column |
| **2NF** | 1NF + no partial dependency on a composite key | `order_items(order_id, product_id, product_name)` — name depends only on product_id |
| **3NF** | 2NF + no transitive dependency | `orders(customer_id, customer_email)` — email depends on customer |
| BCNF | every determinant is a candidate key | rare edge cases |

## Anomalies normalization prevents
- **Update**: change customer email in 300 order rows.
- **Insert**: can't add a product until someone orders it.
- **Delete**: deleting last order loses the customer.

## When to denormalize
- Snapshotting historical values: `order_items.unit_price` (price at time of order — *intentional*!)
- Read-heavy aggregates: cached counters, materialized views
- Analytics: star schemas (facts + dimensions)

## Related
- [[SQL Example Schema]] · [[SQL DDL and Constraints]]
