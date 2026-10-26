---
tags: [javascript, concept]
aliases: [let const var, hoisting, TDZ]
created: 2026-10-26
status: evergreen
---
# JS Variables and Scope

> [!summary] TL;DR
> Use `const` by default, `let` when you must reassign, never `var`. `let`/`const` are **block-scoped** and sit in a **Temporal Dead Zone** until declared; `var` is **function-scoped** and hoisted as `undefined`.

| | Scope | Hoisted | Re-assign | Re-declare |
|---|---|---|---|---|
| `var` | function | yes, as `undefined` | ✅ | ✅ |
| `let` | block | yes, but TDZ | ✅ | ❌ |
| `const` | block | yes, but TDZ | ❌ | ❌ |

```js
console.log(a); // undefined  (var hoisted)
console.log(b); // ReferenceError (TDZ)
var a = 1;
let b = 2;

const obj = { n: 1 };
obj.n = 2;      // OK — const binds the *reference*, not the value
// obj = {};    // TypeError
```

## Scope chain
Lookups walk outward: block → function → module → global. Inner scopes see outer variables, not vice versa. This is what makes [[JS Functions and Closures|closures]] possible.

> [!warning] The classic loop bug
> ```js
> for (var i = 0; i < 3; i++) setTimeout(() => console.log(i)); // 3 3 3
> for (let i = 0; i < 3; i++) setTimeout(() => console.log(i)); // 0 1 2
> ```
> `let` creates a fresh binding per iteration.

## Related
- [[JS Functions and Closures]]
- [[JS Event Loop]] — why `setTimeout` runs later
- [[Python Functions and Arguments]] — Python has function scope + `nonlocal`/`global`
