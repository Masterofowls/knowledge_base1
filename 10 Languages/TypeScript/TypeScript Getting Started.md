---
tags: [typescript, guide]
aliases: [Learn TypeScript, TS roadmap]
created: 2026-10-26
---
# TypeScript Getting Started

## 1. Install
```bash
npm init -y
npm i -D typescript tsx @types/node
npx tsc --init                 # creates tsconfig.json — turn on "strict"
```
Try without installing: typescriptlang.org/play.

## 2. Hello world
```ts
// src/index.ts
type User = { name: string; age?: number };
const describe = (u: User): string => `${u.name}${u.age ? ` (${u.age})` : ""}`;
console.log(describe({ name: "Ada", age: 36 }));
```
```bash
npx tsx src/index.ts           # run directly
npx tsc --noEmit               # type-check only
npx tsc && node dist/index.js  # compile then run
```

## 3. Learning checklist
- [ ] Basic types, `any` vs `unknown` → [[TS Basic Types]]
- [ ] Interfaces vs type aliases → [[TS Interfaces vs Type Aliases]]
- [ ] Unions & narrowing → [[TS Unions and Narrowing]]
- [ ] Generics → [[TS Generics]]
- [ ] Utility types → [[TS Utility Types]]
- [ ] tsconfig & strict mode → [[TS tsconfig]]
- [ ] Runtime validation with Zod → [[TS Runtime Validation]]
- [ ] Advanced: mapped & conditional types → [[TS Mapped and Conditional Types]]
- [ ] Practise: [[Tutorial - Migrate JavaScript to TypeScript]]

## 4. Beginner mistakes
- Turning off `strict` to silence errors — the errors are the value.
- Using `as` casts on API data instead of validating it.
- Annotating every local variable (let inference work; annotate function boundaries).
- Expecting types to exist at runtime (`if (x instanceof MyInterface)` doesn't work).

## 5. Next steps
[[React Getting Started]] (with TS) · [[Express Getting Started]] (with TS) · [[Jest Getting Started]].

## Resources
- TypeScript Handbook — typescriptlang.org/docs/handbook
- Total TypeScript (Matt Pocock) — free tutorials and exercises
- Type Challenges — github.com/type-challenges/type-challenges
