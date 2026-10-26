---
tags: [typescript, concept]
aliases: [TS types, any vs unknown, never]
created: 2026-10-26
status: evergreen
---
# TS Basic Types

```ts
let id: number = 1;
let name: string = "Ada";
let done: boolean = false;
let big: bigint = 10n;
let tags: string[] = ["a"];             // or Array<string>
let point: [number, number] = [0, 0];   // tuple
let maybe: string | null = null;
let literal: "GET" | "POST" = "GET";    // literal union

function log(msg: string): void { console.log(msg); }
function fail(msg: string): never { throw new Error(msg); }

const config = { port: 3000 } as const; // readonly + literal types
```

## The "top" and "bottom" types
| Type | Meaning | Use |
|---|---|---|
| `any` | turns off checking | avoid; escape hatch |
| `unknown` | could be anything, **must narrow before use** | external input, `catch (e)` |
| `never` | no value possible | exhaustiveness checks, functions that throw |

```ts
function parse(input: unknown) {
  if (typeof input === "string") return input.toUpperCase(); // narrowed
  throw new Error("expected string");
}
```

## Inference
Let TS infer locals; **annotate function boundaries** (params & exported return types).

> [!warning] `as` is a lie you tell the compiler
> `const u = data as User` performs no runtime check. Validate external data instead → [[TS Runtime Validation]].

## Related
- [[JS Data Types and Coercion]]
- [[TS Unions and Narrowing]]
- [[Python Type Hints]]
