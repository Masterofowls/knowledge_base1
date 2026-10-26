---
tags: [jest, testing, concept]
aliases: [jest.fn, jest.spyOn, mock functions]
created: 2026-10-26
status: evergreen
---
# Jest Mock Functions

```ts
const onSave = jest.fn();
render(<Form onSave={onSave} />);
// ...
expect(onSave).toHaveBeenCalledTimes(1);
expect(onSave).toHaveBeenCalledWith({ email: "ada@example.com" });
expect(onSave).toHaveBeenLastCalledWith(expect.objectContaining({ email: expect.any(String) }));
onSave.mock.calls;          // [[arg1, arg2], ...]
onSave.mock.results;        // [{ type: "return", value }]

// Programming behaviour
const getRate = jest.fn()
  .mockReturnValueOnce(1.1)
  .mockReturnValue(1.2);
const fetchUser = jest.fn().mockResolvedValue({ id: 1 });      // async
const failing   = jest.fn().mockRejectedValue(new Error("boom"));
const impl      = jest.fn((a: number, b: number) => a + b);

// Typed mock (TS)
const send = jest.fn<Promise<void>, [string]>();

// Spy on an existing method (keeps real impl unless overridden)
const spy = jest.spyOn(console, "error").mockImplementation(() => {});
// ...
expect(spy).not.toHaveBeenCalled();
spy.mockRestore();
```

## Reset family
| | Clears calls | Removes impl | Restores original |
|---|---|---|---|
| `mockClear()` | ✅ | | |
| `mockReset()` | ✅ | ✅ | |
| `mockRestore()` | ✅ | ✅ | ✅ (spies) |

Config equivalents: `clearMocks`, `resetMocks`, `restoreMocks: true` (recommended).

## Related
- [[Jest Module Mocking]] · [[Test Doubles]] · [[pytest Mocking]]
