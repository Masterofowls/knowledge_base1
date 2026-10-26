---
tags: [jest, testing, snippet]
aliases: [expect, toBe, toEqual, matchers]
created: 2026-10-26
status: evergreen
---
# Jest Matchers

| Matcher | Use for |
|---|---|
| `toBe(x)` | primitives / same reference (`Object.is`) |
| `toEqual(x)` | deep equality (ignores `undefined` props) |
| `toStrictEqual(x)` | deep + types + `undefined` props |
| `toBeNull()` / `toBeUndefined()` / `toBeDefined()` | |
| `toBeTruthy()` / `toBeFalsy()` | |
| `toBeGreaterThan(n)` / `toBeCloseTo(0.3, 5)` | numbers / floats |
| `toMatch(/re/)` | strings |
| `toContain(item)` / `toContainEqual(obj)` | arrays |
| `toHaveLength(n)` | arrays / strings |
| `toHaveProperty("a.b", value)` | objects |
| `toMatchObject(partial)` | subset of object |
| `toThrow(ErrorClass \| /msg/)` | sync errors — wrap in a function! |
| `resolves` / `rejects` | promises |
| `toHaveBeenCalled*` | mocks → [[Jest Mock Functions]] |

```ts
expect(user).toEqual({ id: 1, name: "Ada", createdAt: expect.any(Date) });
expect(res.body).toMatchObject({ status: "ok" });
expect(list).toEqual(expect.arrayContaining([expect.objectContaining({ id: 2 })]));
expect(id).toEqual(expect.stringMatching(/^[0-9a-f-]{36}$/));
expect(() => parse("{")).toThrow(SyntaxError);
expect(value).not.toBeNull();

// custom matcher
expect.extend({
  toBeWithinRange(received: number, floor: number, ceiling: number) {
    const pass = received >= floor && received <= ceiling;
    return { pass, message: () => `expected ${received} to be within [${floor}, ${ceiling}]` };
  },
});
```

> [!warning] `expect(0.1 + 0.2).toBe(0.3)` fails — use `toBeCloseTo`.

## Related
- [[pytest Assertions]] · [[Playwright Assertions]] · [[JS Data Types and Coercion]]
