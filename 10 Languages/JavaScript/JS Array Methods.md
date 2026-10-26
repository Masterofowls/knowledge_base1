---
tags: [javascript, snippet]
aliases: [map filter reduce, array methods]
created: 2026-10-26
status: evergreen
---
# JS Array Methods

| Method | Returns | Mutates? |
|---|---|---|
| `map(fn)` | new array, same length | no |
| `filter(fn)` | new array, subset | no |
| `reduce(fn, init)` | single value | no |
| `find` / `findIndex` / `findLast` | element / index | no |
| `some` / `every` | boolean | no |
| `includes(x)` | boolean | no |
| `flat()` / `flatMap(fn)` | new array | no |
| `slice(a, b)` | copy of range | no |
| `toSorted` / `toReversed` / `toSpliced` / `with` | new array (ES2023) | no |
| `sort` / `reverse` / `splice` / `push` / `pop` / `shift` / `unshift` | — | **yes** |

```js
const orders = [{ total: 30, paid: true }, { total: 70, paid: false }, { total: 20, paid: true }];

const revenue = orders
  .filter(o => o.paid)
  .map(o => o.total)
  .reduce((sum, t) => sum + t, 0);           // 50

const byStatus = Object.groupBy(orders, o => (o.paid ? "paid" : "unpaid"));

[3, 10, 1].sort();                      // [1, 10, 3] ⚠️ string compare!
[3, 10, 1].toSorted((a, b) => a - b);   // [1, 3, 10]
```

> [!warning] `forEach` ignores `await` — use `for...of` for sequential async, or `Promise.all(arr.map(...))` for parallel. See [[JS Promises and Async-Await]].

## Related
- [[Python Comprehensions]] — the Python equivalent
- [[SQL Aggregation and GROUP BY]] — reduce/groupBy in SQL
