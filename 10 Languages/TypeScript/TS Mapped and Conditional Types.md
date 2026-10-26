---
tags: [typescript, concept]
aliases: [mapped types, conditional types, infer, template literal types]
created: 2026-10-26
status: growing
---
# TS Mapped and Conditional Types

## keyof / typeof / indexed access
```ts
const routes = { home: "/", user: "/users/:id" } as const;
type RouteName = keyof typeof routes;          // "home" | "user"
type RoutePath = (typeof routes)[RouteName];   // "/" | "/users/:id"
```

## Mapped types
```ts
type MyPartial<T> = { [K in keyof T]?: T[K] };
type Getters<T> = { [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K] };
// Getters<{name: string}> → { getName: () => string }
```

## Conditional types + infer
```ts
type IsString<T> = T extends string ? true : false;
type ElementOf<T> = T extends (infer U)[] ? U : never;
type Unwrap<T> = T extends Promise<infer U> ? U : T;
```
Conditional types **distribute** over unions: `IsString<string | number>` → `true | false`.

## Template literal types
```ts
type Method = "get" | "post";
type Handler = `on${Capitalize<Method>}`;      // "onGet" | "onPost"
type Param<S> = S extends `${string}:${infer P}` ? P : never;
type X = Param<"/users/:id">;                   // "id"
```

> [!warning] Type-level cleverness is a maintenance cost. Use it in library/shared code; keep app code simple.

## Related
- [[TS Generics]] · [[TS Utility Types]]
