---
tags: [javascript, concept]
aliases: [closure, closures, arrow functions]
created: 2026-10-26
status: evergreen
---
# JS Functions and Closures

> [!summary] TL;DR
> A **closure** is a function bundled with the variables of the scope it was *created* in. It "remembers" them even after that scope has returned.

## Function forms
```js
function declared(a, b = 2) { return a + b; }   // hoisted
const expr = function (a) { return a; };         // not hoisted
const arrow = (a) => a * 2;                      // no own `this`/`arguments`
const rest = (...args) => args.length;
```

## Closure example
```js
function makeCounter() {
  let count = 0;                 // private state
  return {
    inc: () => ++count,
    get: () => count,
  };
}
const c = makeCounter();
c.inc(); c.inc();
c.get(); // 2  — `count` lives on in the closure
```

## Uses
- Data privacy / module pattern
- Function factories & partial application
- Memoization
- Event handlers & callbacks capturing state
- React hooks rely on closures → see [[React useEffect]]

```js
const memo = (fn) => {
  const cache = new Map();
  return (x) => {
    if (!cache.has(x)) cache.set(x, fn(x));
    return cache.get(x);
  };
};
```

> [!warning] Stale closures
> A closure captures the binding from when it was created. In React, a callback created on render N sees render N's state unless re-created. See [[React useEffect]].

## Related
- [[JS Variables and Scope]]
- [[JS this Keyword]] — arrow functions inherit `this`
- [[Python Decorators]] — Python closures in action
