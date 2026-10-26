---
tags: [javascript, snippet]
aliases: [destructuring, spread, rest operator]
created: 2026-10-26
status: evergreen
---
# JS Destructuring and Spread

```js
// Objects
const { id, name: userName, role = "user", ...rest } = user;

// Arrays
const [first, , third, ...others] = [1, 2, 3, 4, 5];
[a, b] = [b, a];                          // swap

// Nested + defaults
const { address: { city } = {} } = user;

// Function params
function connect({ host = "localhost", port = 5432 } = {}) {}

// Spread
const merged = { ...defaults, ...overrides };   // later wins
const copy = [...arr, newItem];
Math.max(...nums);
```

> [!tip] Immutable updates (React state)
> ```js
> setUser(u => ({ ...u, address: { ...u.address, city: "Oslo" } }));
> setItems(xs => xs.filter(x => x.id !== id));
> ```
> See [[React State and Props]].

> [!warning] Spread is shallow — nested objects are still shared. Use `structuredClone` for deep copies.

## Related
- [[JS Data Types and Coercion]]
- [[Python Data Types and Collections]] — Python unpacking `a, *rest = xs`, `{**d1, **d2}`
