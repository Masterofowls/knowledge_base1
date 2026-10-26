---
tags: [jest, testing, concept]
aliases: [snapshots, toMatchSnapshot, toMatchInlineSnapshot]
created: 2026-10-26
status: evergreen
---
# Jest Snapshot Testing

```ts
test("serializes order", () => {
  expect(serialize(order)).toMatchSnapshot();          // stored in __snapshots__/*.snap
});

test("error message", () => {
  expect(() => validate({})).toThrowErrorMatchingInlineSnapshot(`"email is required"`);
});

test("dynamic fields", () => {
  expect(user).toMatchSnapshot({ id: expect.any(Number), createdAt: expect.any(Date) });
});
```

```bash
npx jest -u            # update snapshots (review the diff in git!)
npx jest --ci          # CI: missing snapshots fail instead of being written
```

> [!warning] When snapshots go wrong
> Huge component snapshots get rubber-stamped with `-u` and catch nothing. Keep them **small and focused** (a serialized value, an error message), prefer inline snapshots, and use explicit assertions for behaviour.

Python equivalent: **syrupy** ([[pytest Plugins]]). Visual snapshots: Playwright `toHaveScreenshot()` ([[Playwright Assertions]]).

## Related
- [[Jest Matchers]]
