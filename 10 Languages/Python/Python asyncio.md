---
tags: [python, concept]
aliases: [async await python, asyncio, coroutines]
created: 2026-10-26
status: evergreen
---
# Python asyncio

> [!summary] TL;DR
> `async def` creates a **coroutine**; nothing runs until it's awaited or scheduled on the **event loop**. Concurrency is cooperative: a coroutine only yields control at `await`. Great for I/O-bound work; useless for CPU-bound.

```python
import asyncio, httpx

async def fetch(client: httpx.AsyncClient, url: str) -> int:
    r = await client.get(url, timeout=10)
    return r.status_code

async def main():
    async with httpx.AsyncClient() as client:
        # concurrent — structured concurrency (3.11+)
        async with asyncio.TaskGroup() as tg:
            tasks = [tg.create_task(fetch(client, u)) for u in URLS]
        print([t.result() for t in tasks])

        # or gather
        codes = await asyncio.gather(*(fetch(client, u) for u in URLS), return_exceptions=True)

        # timeout
        async with asyncio.timeout(5):
            await slow()

asyncio.run(main())
```

## Limiting concurrency
```python
sem = asyncio.Semaphore(10)
async def limited(u):
    async with sem:
        return await fetch(client, u)
```

> [!warning] Blocking calls (`time.sleep`, `requests.get`, sync DB drivers) inside `async def` freeze the whole loop. Use async libs or `await asyncio.to_thread(blocking_fn)`.

| JS | Python |
|---|---|
| `Promise` | `Task` / `Future` |
| `Promise.all` | `asyncio.gather` / `TaskGroup` |
| `Promise.race` | `asyncio.wait(..., return_when=FIRST_COMPLETED)` |
| `setTimeout` | `loop.call_later` / `asyncio.sleep` |
| microtask queue | ready queue |

## Related
- [[Async Programming Concepts]] · [[JS Promises and Async-Await]] · [[JS Event Loop]]
- [[FastAPI Async and Concurrency]]
- [[pytest Plugins]] — `pytest-asyncio` / `anyio`
