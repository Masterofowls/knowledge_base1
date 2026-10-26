---
tags: [javascript, concept]
aliases: [prototype chain, JS classes, inheritance]
created: 2026-10-26
status: evergreen
---
# JS Prototypes and Classes

> [!summary] TL;DR
> Every object has a hidden link (`[[Prototype]]`) to another object. Property lookups walk this **prototype chain**. `class` is syntax sugar over prototypes.

```js
class Animal {
  #secret = 42;                 // private field
  static kingdom = "Animalia";  // on the constructor
  constructor(name) { this.name = name; }
  speak() { return `${this.name} makes a sound`; }
  get upper() { return this.name.toUpperCase(); }
}

class Dog extends Animal {
  speak() { return `${super.speak()} — woof`; }
}

const d = new Dog("Rex");
Object.getPrototypeOf(d) === Dog.prototype;           // true
Object.getPrototypeOf(Dog.prototype) === Animal.prototype; // true
d.hasOwnProperty("speak");                           // false — found on prototype
```

## Chain
`d → Dog.prototype → Animal.prototype → Object.prototype → null`

## Composition over inheritance
```js
const canFly = (o) => ({ ...o, fly: () => `${o.name} flies` });
const bird = canFly({ name: "Tweety" });
```

> [!tip] Prefer composition and plain objects/functions; reserve classes for things with identity and lifecycle (e.g. Page Objects in [[Playwright Page Object Model]]).

## Related
- [[JS this Keyword]]
- [[TS Interfaces vs Type Aliases]] — structural typing for classes
- [[Python Classes and Dataclasses]]
