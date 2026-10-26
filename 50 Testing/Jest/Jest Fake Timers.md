---
tags: [jest, testing, snippet]
aliases: [Jest Timers and Mocks, useFakeTimers, fake timers]
created: 2026-10-26
status: evergreen
---
# Jest Fake Timers

```ts
beforeEach(() => jest.useFakeTimers());
afterEach(() => jest.useRealTimers());

test("debounce calls once after 300ms", () => {
  const fn = jest.fn();
  const debounced = debounce(fn, 300);
  debounced(); debounced(); debounced();
  expect(fn).not.toHaveBeenCalled();
  jest.advanceTimersByTime(300);
  expect(fn).toHaveBeenCalledTimes(1);
});

test("polls until ready", async () => {
  const p = waitUntilReady();
  await jest.advanceTimersByTimeAsync(5_000);   // also flushes promises between timers
  await expect(p).resolves.toBe(true);
});

test("date-dependent logic", () => {
  jest.setSystemTime(new Date("2026-12-31T23:59:59Z"));
  expect(isNewYearsEve()).toBe(true);
});
```

API: `runAllTimers()`, `runOnlyPendingTimers()`, `advanceTimersByTime(ms)`, `advanceTimersToNextTimer()`, `getTimerCount()`, `now()`.

> [!warning] Mixing fake timers with real promises: use the `...Async` variants, or `await Promise.resolve()` to flush microtasks. See [[JS Event Loop]].

## Related
- [[Jest Async Testing]] · [[Test Isolation and Flakiness]] · [[Playwright Network Mocking]] (`page.clock`)
