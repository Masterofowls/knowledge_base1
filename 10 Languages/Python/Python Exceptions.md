---
tags: [python, concept]
aliases: [try except, raise, exception handling]
created: 2026-10-26
status: evergreen
---
# Python Exceptions

```python
class AppError(Exception):
    """Base for this app's errors."""

class NotFound(AppError):
    def __init__(self, resource: str, id: int):
        super().__init__(f"{resource} {id} not found")
        self.resource, self.id = resource, id

try:
    user = repo.get(42)
except NotFound as e:
    log.warning("missing: %s", e)
except (TimeoutError, ConnectionError):
    retry()
else:
    print("ran only if no exception")
finally:
    cleanup()

# chaining keeps the original traceback
try:
    int("x")
except ValueError as e:
    raise AppError("bad config") from e

# 3.11+: exception groups
try:
    async with asyncio.TaskGroup() as tg: ...
except* ValueError as eg:
    ...
```

> [!tip] EAFP — "Easier to Ask Forgiveness than Permission" is idiomatic: try the operation and catch the specific error rather than pre-checking.

> [!warning] Never bare `except:` — it catches `KeyboardInterrupt`/`SystemExit`. Catch the narrowest exception.

## Related
- [[Python Context Managers]] — guaranteed cleanup
- [[pytest Assertions]] — `pytest.raises`
- [[FastAPI Error Handling]] · [[JS Error Handling]]
