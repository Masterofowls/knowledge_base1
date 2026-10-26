---
tags: [typescript, concept]
aliases: [generics]
created: 2026-10-26
status: evergreen
---
# TS Generics

> [!summary] TL;DR
> Generics are **type parameters** — they let a function/type work over many types while preserving the relationship between input and output.

```ts
function first<T>(xs: T[]): T | undefined { return xs[0]; }
first([1, 2]);        // number | undefined (T inferred)

// Constraints
function longest<T extends { length: number }>(a: T, b: T): T {
  return a.length >= b.length ? a : b;
}

// keyof constraint — type-safe property access
function get<T, K extends keyof T>(obj: T, key: K): T[K] { return obj[key]; }
get({ id: 1, name: "a" }, "name");   // string
// get(user, "nope");               // ❌ compile error

// Defaults
type ApiResponse<T = unknown> = { data: T; error?: string };

// Generic class
class Repo<T extends { id: number }> {
  private items = new Map<number, T>();
  save(item: T) { this.items.set(item.id, item); }
  find(id: number) { return this.items.get(id); }
}
```

> [!tip] If a type parameter appears only once in a signature, you probably don't need it.

## Related
- [[TS Utility Types]] — built from generics
- [[TS Mapped and Conditional Types]]
- [[Python Type Hints]] — `TypeVar`, PEP 695 `def f[T](x: T) -> T`
