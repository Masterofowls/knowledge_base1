---
tags: [javascript, concept]
aliases: [this, bind call apply]
created: 2026-10-26
status: evergreen
---
# JS this Keyword

> [!summary] TL;DR
> `this` is decided by **how a function is called**, not where it is defined — except arrow functions, which take `this` from the enclosing scope.

## Binding rules (highest priority first)
1. **`new`** — `new Foo()` → `this` is the new object.
2. **Explicit** — `fn.call(obj, a)`, `fn.apply(obj, [a])`, `fn.bind(obj)`.
3. **Implicit** — `obj.method()` → `this === obj`.
4. **Default** — plain `fn()` → `undefined` in strict mode / modules (global object in sloppy mode).

**Arrow functions** ignore all four and use lexical `this`.

```js
const user = {
  name: "Ada",
  greet() { return `Hi ${this.name}`; },
  greetLater() { setTimeout(() => console.log(this.name), 0); }, // arrow keeps `this`
};

const g = user.greet;
g();                       // TypeError / undefined — lost implicit binding
const bound = user.greet.bind(user);
bound();                   // "Hi Ada"
```

> [!warning] Passing methods as callbacks loses `this`
> `button.addEventListener("click", obj.handle)` → `this` is the button. Use an arrow wrapper or `.bind`, or class fields: `handle = () => {...}`.

## Related
- [[JS Prototypes and Classes]]
- [[JS Functions and Closures]]
- [[Python Classes and Dataclasses]] — Python passes `self` explicitly; no such ambiguity
