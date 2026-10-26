---
tags: [javascript, concept]
aliases: [Promise, async await, Promise.all]
created: 2026-10-26
status: evergreen
---
# JS Promises and Async-Await

> [!summary] TL;DR
> A **Promise** is a placeholder for a future value: *pending → fulfilled | rejected*. `async` functions always return a promise; `await` pauses the function (not the thread) until the promise settles.

```js
const sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

async function loadDashboard(userId) {
  const user = await getUser(userId);                  // sequential (dependent)
  const [orders, prefs] = await Promise.all([          // parallel (independent)
    getOrders(user.id),
    getPrefs(user.id),
  ]);
  return { user, orders, prefs };
}
```

## Combinators
| | Resolves when | Rejects when |
|---|---|---|
| `Promise.all` | all fulfil | **first** rejects |
| `Promise.allSettled` | all settle (never rejects) | — |
| `Promise.race` | first settles | first settles with rejection |
| `Promise.any` | first fulfils | all reject (`AggregateError`) |

## Timeouts & cancellation
```js
const res = await fetch(url, { signal: AbortSignal.timeout(5000) });

const ctrl = new AbortController();
fetch(url, { signal: ctrl.signal });
ctrl.abort();
```

> [!warning] Common mistakes
> - Sequential `await` in a loop when calls are independent → slow. Use `Promise.all`.
> - `arr.forEach(async x => await f(x))` — nothing waits for it.
> - Forgetting `return`/`await` → the caller can't catch the error.

## Related
- [[JS Event Loop]]
- [[JS Error Handling]]
- [[Python asyncio]] · [[Async Programming Concepts]]
- [[Jest Async Testing]]
