---
tags: [python, concept]
aliases: [with statement, contextmanager]
created: 2026-10-26
status: evergreen
---
# Python Context Managers

> [!summary] TL;DR
> `with` guarantees setup/teardown (`__enter__`/`__exit__`) even when exceptions occur. Use for files, locks, DB transactions, temp state.

```python
with open("data.json") as f, open("out.json", "w") as out:
    out.write(f.read())

# Class-based
class Timer:
    def __enter__(self):
        self.start = time.perf_counter(); return self
    def __exit__(self, exc_type, exc, tb):
        self.elapsed = time.perf_counter() - self.start
        return False            # don't suppress exceptions

# Generator-based (simpler)
from contextlib import contextmanager, suppress

@contextmanager
def transaction(conn):
    try:
        yield conn
        conn.commit()
    except Exception:
        conn.rollback()
        raise

with suppress(FileNotFoundError):
    os.remove("tmp.txt")
```

Async version: `async with` + `@asynccontextmanager` → see [[Python asyncio]], [[FastAPI Dependency Injection]] (yield deps).

## Related
- [[SQL Transactions and Isolation]]
- [[pytest Fixtures]]
