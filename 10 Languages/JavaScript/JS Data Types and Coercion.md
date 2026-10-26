---
tags: [javascript, concept]
aliases: [JS types, type coercion, == vs ===]
created: 2026-10-26
status: evergreen
---
# JS Data Types and Coercion

> [!summary] TL;DR
> 7 primitives (`string`, `number`, `bigint`, `boolean`, `undefined`, `null`, `symbol`) + `object` (arrays, functions, dates are objects). Primitives are copied by value; objects by reference. Always use `===`.

## typeof quirks
```js
typeof null        // "object"  ← historical bug
typeof []          // "object"  → use Array.isArray([])
typeof (() => {})  // "function"
typeof NaN         // "number"  → use Number.isNaN(x)
```

## Truthy / falsy
Falsy values: `false, 0, -0, 0n, "", null, undefined, NaN`. **Everything else is truthy**, including `[]`, `{}`, `"0"`.

## Coercion
```js
"5" + 1   // "51"  (+ prefers strings)
"5" - 1   // 4     (- coerces to number)
0 == ""   // true   😬
0 === ""  // false  ✅
null == undefined  // true — the one handy use of ==
0.1 + 0.2 === 0.3  // false — IEEE 754 floats
```

## Nullish vs falsy defaults
```js
const port = config.port ?? 3000;   // only null/undefined → 3000
const name = input || "anon";        // "" and 0 also → "anon"
user?.address?.city                  // optional chaining
```

## Copying objects
```js
const shallow = { ...obj };
const deep = structuredClone(obj);   // modern deep copy
```

## Related
- [[JS Destructuring and Spread]]
- [[TS Basic Types]]
- [[Type Systems Compared]]
