---
tags: [python, snippet]
aliases: [list comprehension, dict comprehension, generator expression]
created: 2026-10-26
status: evergreen
---
# Python Comprehensions

```python
squares   = [x * x for x in range(10)]
evens     = [x for x in nums if x % 2 == 0]
labels    = ["even" if x % 2 == 0 else "odd" for x in nums]   # if/else goes BEFORE for
by_id     = {u.id: u for u in users}
unique    = {w.lower() for w in words}
flat      = [cell for row in grid for cell in row]            # nested: outer loop first
total     = sum(o.total for o in orders if o.paid)            # generator expression — lazy, no list
```

| JS | Python |
|---|---|
| `arr.map(f)` | `[f(x) for x in arr]` |
| `arr.filter(p)` | `[x for x in arr if p(x)]` |
| `arr.reduce(...)` | `sum()`, `max()`, `functools.reduce` |
| `arr.some(p)` / `every(p)` | `any(...)` / `all(...)` |
| `Object.groupBy` | `itertools.groupby` (sorted input!) or `defaultdict(list)` |

> [!tip] If a comprehension needs more than one `if` and nesting, write a loop. Readability wins.

## Related
- [[JS Array Methods]]
- [[Python Generators and Iterators]]
