---
tags: [sql, security, concept]
aliases: [SQL injection, prepared statements, bind parameters]
created: 2026-10-26
status: evergreen
---
# SQL Injection and Parameterized Queries

> [!danger] Never build SQL by concatenating/formatting user input.

```python
# ❌ email = "x' OR '1'='1"  → dumps the table
cur.execute(f"SELECT * FROM customers WHERE email = '{email}'")

# ✅ parameters are sent separately from the SQL text
cur.execute("SELECT * FROM customers WHERE email = %s", (email,))     # psycopg
conn.execute(text("SELECT * FROM customers WHERE email = :e"), {"e": email})   # SQLAlchemy
Customer.objects.raw("SELECT * FROM customers WHERE email = %s", [email])      # Django
```

```js
// node-postgres
await pool.query("SELECT * FROM customers WHERE email = $1", [email]);
```

Identifiers (table/column names, `ORDER BY` direction) can't be parameters → **whitelist** them:
```python
sort = {"name": "name", "date": "created_at"}.get(user_sort, "created_at")
```

ORMs parameterize for you — until you use `.raw()`, `.extra()`, or `text()` with f-strings.

## Related
- [[SQL MOC]] · [[Authentication Schemes]]
