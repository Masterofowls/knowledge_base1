---
tags: [python, concept]
aliases: [decorators, functools.wraps]
created: 2026-10-26
status: evergreen
---
# Python Decorators

> [!summary] TL;DR
> A decorator is a function that takes a function and returns a (usually wrapped) function. `@dec` above `def f` means `f = dec(f)`.

```python
import functools, time

def timed(fn):
    @functools.wraps(fn)                 # keep __name__, __doc__
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        try:
            return fn(*args, **kwargs)
        finally:
            print(f"{fn.__name__} took {time.perf_counter() - start:.3f}s")
    return wrapper

# Decorator with arguments = a factory returning a decorator
def retry(times: int = 3, exceptions=(Exception,)):
    def decorator(fn):
        @functools.wraps(fn)
        def wrapper(*args, **kwargs):
            for attempt in range(1, times + 1):
                try:
                    return fn(*args, **kwargs)
                except exceptions:
                    if attempt == times:
                        raise
        return wrapper
    return decorator

@retry(times=5, exceptions=(ConnectionError,))
@timed
def fetch(url): ...
```

## Built-in / stdlib decorators
`@property`, `@classmethod`, `@staticmethod`, `@functools.cache`, `@functools.lru_cache(maxsize=128)`, `@dataclass`, `@contextlib.contextmanager`.

## In frameworks
- [[FastAPI Routing and Parameters|FastAPI]]: `@app.get("/users")`
- [[pytest Fixtures|pytest]]: `@pytest.fixture`, `@pytest.mark.parametrize`
- [[Django Views|Django]]: `@login_required`

## Related
- [[Python Functions and Arguments]]
- [[JS Functions and Closures]]
