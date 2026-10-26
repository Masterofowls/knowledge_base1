---
tags: [javascript, concept]
aliases: [event loop, microtask, macrotask, call stack]
created: 2026-10-26
status: evergreen
---
# JS Event Loop

> [!summary] TL;DR
> JS runs on **one thread**. The event loop: run the current task to completion → drain **all microtasks** (promise callbacks, `queueMicrotask`) → render (browser) → take the next **macrotask** (timers, I/O, events).

```
┌───────────────┐
│  Call stack    │ ← runs synchronous code
└──────┬────────┘
       │ empty?
┌──────▼────────┐  drain ALL  ┌──────────────────┐
│ Microtask queue│◄────────────│ Promise.then, await│
└──────┬────────┘             └──────────────────┘
       │ then ONE
┌──────▼────────┐             ┌──────────────────┐
│ Macrotask queue│◄────────────│ setTimeout, I/O,  │
└───────────────┘             │ events, MessageChannel │
                              └──────────────────┘
```

## Classic quiz
```js
console.log("1");
setTimeout(() => console.log("2"), 0);
Promise.resolve().then(() => console.log("3"));
queueMicrotask(() => console.log("4"));
console.log("5");
// → 1 5 3 4 2
```

## Node specifics
Node's loop has phases (timers → pending → poll → check → close). `process.nextTick` runs before promise microtasks; `setImmediate` runs in the check phase.

> [!warning] Blocking the loop
> CPU-heavy sync work (big JSON parse, tight loops) freezes UI / stalls every request in a Node server. Offload to Worker threads or chunk the work.

## Related
- [[JS Promises and Async-Await]]
- [[Async Programming Concepts]] — compared with Python asyncio
- [[Jest Timers and Mocks]] — fake timers
