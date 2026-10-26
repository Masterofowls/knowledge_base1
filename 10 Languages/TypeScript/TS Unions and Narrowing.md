---
tags: [typescript, concept]
aliases: [type guards, discriminated unions, narrowing]
created: 2026-10-26
status: evergreen
---
# TS Unions and Narrowing

> [!summary] TL;DR
> A union `A | B` can be either; TS lets you use only what's common until you **narrow** it with a runtime check it understands.

## Narrowing tools
```ts
function fmt(x: string | number | Date | null) {
  if (x === null) return "-";                 // equality
  if (typeof x === "string") return x;        // typeof
  if (x instanceof Date) return x.toISOString(); // instanceof
  return x.toFixed(2);                        // number
}

if ("swim" in animal) animal.swim();          // `in`
```

## Discriminated unions (⭐ most useful pattern)
```ts
type Result<T> =
  | { status: "loading" }
  | { status: "success"; data: T }
  | { status: "error"; error: string };

function render(r: Result<User>) {
  switch (r.status) {
    case "loading": return "…";
    case "success": return r.data.name;    // data available here
    case "error":   return r.error;
    default: {
      const _exhaustive: never = r;          // compile error if a case is missed
      return _exhaustive;
    }
  }
}
```

## Custom type guards
```ts
function isUser(x: unknown): x is User {
  return typeof x === "object" && x !== null && "id" in x;
}
// Assertion function
function assertDefined<T>(v: T): asserts v is NonNullable<T> {
  if (v == null) throw new Error("undefined!");
}
```

## Related
- [[TS Basic Types]]
- [[React with TypeScript]] — modelling component state
- [[Python Type Hints]] — `Literal`, `TypeGuard`, `match`
