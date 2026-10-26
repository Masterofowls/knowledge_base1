---
tags: [fastapi, concept]
aliases: [async def vs def fastapi]
created: 2026-10-26
status: evergreen
---
# FastAPI Async and Concurrency

> [!summary] TL;DR
> - `async def` endpoint → runs **on the event loop**. Must only `await` non-blocking I/O.
> - plain `def` endpoint → FastAPI runs it **in a threadpool**, so blocking code is OK.
> The worst case is blocking code inside `async def`: it stalls *every* request.

| Your code uses… | Declare |
|---|---|
| async libs (`httpx.AsyncClient`, `asyncpg`, SQLAlchemy async) | `async def` |
| sync libs (`requests`, sync SQLAlchemy, heavy file ops) | `def` |
| CPU-heavy work | `def` + offload to a worker queue / process pool |

```python
@app.get("/slow-bad")
async def bad():
    time.sleep(2)                # ❌ blocks the event loop
    return {}

@app.get("/slow-ok")
def ok():
    time.sleep(2)                # ✅ threadpool
    return {}

@app.get("/mixed")
async def mixed():
    result = await asyncio.to_thread(legacy_sync_call)   # ✅ offload
    return result
```

## Background work
```python
from fastapi import BackgroundTasks
@app.post("/signup")
async def signup(user: UserCreate, bg: BackgroundTasks):
    u = await create(user)
    bg.add_task(send_welcome_email, u.email)   # runs after response is sent
    return u
```
For durable/retryable jobs use Celery, RQ, Dramatiq, arq.

## Related
- [[Python asyncio]] · [[Async Programming Concepts]]
