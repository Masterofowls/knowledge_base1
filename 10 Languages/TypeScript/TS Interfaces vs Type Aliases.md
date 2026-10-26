---
tags: [typescript, comparison]
aliases: [interface vs type]
created: 2026-10-26
status: evergreen
---
# TS Interfaces vs Type Aliases

```ts
interface User {
  readonly id: number;
  name: string;
  email?: string;                  // optional
  [key: string]: unknown;          // index signature
}
interface Admin extends User { permissions: string[] }

type Point = { x: number; y: number };
type ID = string | number;         // only `type` can alias unions/primitives
type Admin2 = User & { permissions: string[] };   // intersection
```

| Feature | `interface` | `type` |
|---|---|---|
| Object shapes | ✅ | ✅ |
| Unions, primitives, tuples | ❌ | ✅ |
| `extends` | ✅ | via `&` |
| Declaration merging | ✅ (re-open) | ❌ |
| Mapped/conditional types | ❌ | ✅ |

> [!tip] Rule of thumb
> `interface` for public object contracts (libraries, classes implementing them); `type` for everything else. Consistency within a codebase matters more than the choice.

## Structural typing
Compatibility is by **shape**, not name:
```ts
interface Named { name: string }
const dog = { name: "Rex", breed: "lab" };
const n: Named = dog;   // OK — has `name`
```

## Related
- [[TS Utility Types]]
- [[JS Prototypes and Classes]]
- [[Python Classes and Dataclasses]] — `Protocol` is Python's structural typing
