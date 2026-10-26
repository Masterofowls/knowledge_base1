---
tags: [concept, javascript, python, comparison]
aliases: [concurrency vs parallelism, event loop comparison]
created: 2026-10-26
status: evergreen
---
# Async Programming Concepts

> [!summary] TL;DR
> **Concurrency** = dealing with many things at once (interleaving). **Parallelism** = doing many things at once (multiple cores). JS and Python `async` give *concurrency on one thread* via an **event loop** — ideal for I/O-bound work.

## Models
| Model | Examples | Good for |
|---|---|---|
| Threads (preemptive) | Python `threading`, Java | blocking I/O with sync libs |
| Processes | `multiprocessing`, worker pools | CPU-bound work |
| Event loop + coroutines (cooperative) | JS, Python `asyncio` | many concurrent I/O ops |
| Actors / green threads | Erlang, Go goroutines | massive concurrency |

## JS vs Python side by side
| | JavaScript | Python |
|---|---|---|
| Loop | always running (browser/Node) | started explicitly: `asyncio.run(main())` |
| Calling `async fn()` | starts running immediately, returns Promise | creates coroutine object, **nothing runs** until awaited/scheduled |
| Fan-out | `Promise.all([...])` | `asyncio.gather(...)` / `TaskGroup` |
| Timeout | `AbortSignal.timeout(ms)` | `asyncio.timeout(s)` |
| Cancellation | `AbortController` | `task.cancel()` → `CancelledError` |
| Blocking the loop | sync CPU work | sync CPU work **or** sync I/O (`requests`, `time.sleep`) |
| Offload | Worker threads | `asyncio.to_thread`, process pool |

## Rules of thumb
1. Never block the event loop.
2. Run independent awaits concurrently; dependent ones sequentially.
3. Always bound concurrency (semaphore / p-limit) against external services.
4. Always set timeouts on network calls.
5. Propagate cancellation; clean up in `finally`.

## Related
- [[JS Event Loop]] · [[JS Promises and Async-Await]] · [[Python asyncio]] · [[FastAPI Async and Concurrency]]
