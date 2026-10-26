---
tags: [tutorial, javascript]
aliases: [JS exercises, async exercises]
created: 2026-10-26
level: beginner
time: 1-2h
---
# Tutorial - Async JavaScript Exercises

> [!abstract] What you'll practise
> Predicting output of the event loop, writing promise utilities by hand (`sleep`, `timeout`, `retry`, concurrency limit), and fixing common async bugs. Run everything with `node file.mjs`.

**Notes used:** [[JS Event Loop]], [[JS Promises and Async-Await]], [[JS Functions and Closures]].

## Exercise 1 — Predict the output
```js
console.log("A");
setTimeout(() => console.log("B"), 0);
Promise.resolve().then(() => console.log("C")).then(() => console.log("D"));
(async () => { console.log("E"); await null; console.log("F"); })();
console.log("G");
```
> [!success]- Answer
> `A E G C F D B` — synchronous code first (A, E, G; the async function runs synchronously until its first `await`), then all microtasks in order of queueing (C, F, then D which was queued when C ran), then the macrotask (B).

## Exercise 2 — `sleep` and `timeout`
Write `sleep(ms)` and `withTimeout(promise, ms)` which rejects with `Error("Timeout")` if the promise takes too long.
> [!success]- Solution
> ```js
> const sleep = (ms) => new Promise((r) => setTimeout(r, ms));
> function withTimeout(promise, ms) {
>   let id;
>   const timer = new Promise((_, reject) => { id = setTimeout(() => reject(new Error("Timeout")), ms); });
>   return Promise.race([promise, timer]).finally(() => clearTimeout(id));
> }
> await withTimeout(sleep(50), 100);   // ok
> await withTimeout(sleep(500), 100).catch((e) => console.log(e.message)); // Timeout
> ```

## Exercise 3 — `retry` with exponential backoff
`retry(fn, { retries: 3, baseMs: 100 })` calls `fn` until it resolves, waiting 100, 200, 400 ms between attempts, then rethrows the last error.
> [!success]- Solution
> ```js
> async function retry(fn, { retries = 3, baseMs = 100 } = {}) {
>   for (let attempt = 0; ; attempt++) {
>     try { return await fn(attempt); }
>     catch (err) {
>       if (attempt >= retries) throw err;
>       await sleep(baseMs * 2 ** attempt + Math.random() * 50);   // jitter
>     }
>   }
> }
> let n = 0;
> console.log(await retry(async () => { if (++n < 3) throw new Error("flaky"); return "ok"; }));
> ```

## Exercise 4 — Concurrency limit
Fetch 20 URLs but never more than 4 at a time. Write `mapLimit(items, limit, asyncFn)` returning results in input order.
> [!success]- Solution
> ```js
> async function mapLimit(items, limit, fn) {
>   const results = new Array(items.length);
>   let next = 0;
>   async function worker() {
>     while (next < items.length) {
>       const i = next++;                       // safe: single-threaded, no await between read & increment
>       results[i] = await fn(items[i], i);
>     }
>   }
>   await Promise.all(Array.from({ length: Math.min(limit, items.length) }, worker));
>   return results;
> }
> const ids = Array.from({ length: 20 }, (_, i) => i + 1);
> const posts = await mapLimit(ids, 4, (id) => fetch(`https://jsonplaceholder.typicode.com/posts/${id}`).then(r => r.json()));
> console.log(posts.length);
> ```

## Exercise 5 — Find the bugs
```js
async function saveAll(users) {
  users.forEach(async (u) => { await db.save(u); });
  console.log("all saved");
}

async function getTotal(ids) {
  let total = 0;
  for (const id of ids) {
    const order = await fetchOrder(id);
    total += order.amount;
  }
  return total;
}

function load() {
  fetchConfig().then((c) => { if (!c.ok) throw new Error("bad config"); });
}
```
> [!success]- Answers
> 1. `forEach` doesn't await → "all saved" logs before anything is saved, and errors are unhandled. Use `await Promise.all(users.map((u) => db.save(u)))` or `for...of`.
> 2. Correct but slow: sequential requests for independent data. `const orders = await Promise.all(ids.map(fetchOrder)); return orders.reduce((s, o) => s + o.amount, 0);`
> 3. The promise isn't returned → the caller can't await it and the thrown error becomes an unhandled rejection. `return fetchConfig().then(...)`.

## Exercise 6 — Debounce with closures
Write `debounce(fn, ms)` so that rapid calls only run `fn` once, `ms` after the last call, with the last arguments.
> [!success]- Solution
> ```js
> function debounce(fn, ms) {
>   let id;
>   return (...args) => { clearTimeout(id); id = setTimeout(() => fn(...args), ms); };
> }
> ```
> Test it with fake timers → [[Jest Fake Timers]].

## Next
Turn these into a tested module: put the functions in `async-utils.js` and write Jest tests for each ([[Jest Async Testing]]).

## Related
- [[Tutorials MOC]] · [[Async Programming Concepts]] · [[Python asyncio]]
