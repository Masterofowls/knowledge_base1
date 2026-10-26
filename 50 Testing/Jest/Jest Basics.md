---
tags: [jest, testing, concept]
aliases: [describe test it, test.each]
created: 2026-10-26
status: evergreen
---
# Jest Basics

```ts
// src/cart.test.ts
import { Cart } from "./cart";

describe("Cart", () => {
  test("empty cart total is 0", () => {
    expect(new Cart().total()).toBe(0);
  });

  it("applies VIP discount", () => {         // `it` is an alias of `test`
    const cart = new Cart({ vip: true });
    cart.add({ price: 100 });
    expect(cart.total()).toBe(90);
  });

  test.each([
    ["Hello World", "hello-world"],
    ["  trim  ", "trim"],
  ])("slugify(%s) → %s", (input, expected) => {
    expect(slugify(input)).toBe(expected);
  });

  test.each`
    a    | b    | sum
    ${1} | ${2} | ${3}
    ${2} | ${2} | ${4}
  `("$a + $b = $sum", ({ a, b, sum }) => expect(a + b).toBe(sum));

  test.todo("handles currency conversion");
  test.skip("temporarily disabled", () => {});
  // test.only(...) — run only this (never commit!)
});
```

Discovery: files in `__tests__/` or named `*.test.[jt]s(x)` / `*.spec.[jt]s(x)`.

```bash
npx jest                          # all
npx jest cart                     # path pattern
npx jest -t "VIP"                 # test name pattern
npx jest --watch                  # re-run on change (git-aware)
npx jest --coverage
npx jest --runInBand              # serial (debugging, shared DB)
npx jest --onlyChanged / --changedSince=main
npx jest --detectOpenHandles      # find leaked servers/timers
```

## Related
- [[Jest Matchers]] · [[pytest Basics]] · [[pytest Parametrize]] · [[Arrange-Act-Assert]]
