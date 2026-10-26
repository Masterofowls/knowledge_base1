---
tags: [fastapi, snippet]
aliases: [HTTPException, exception handlers]
created: 2026-10-26
status: evergreen
---
# FastAPI Error Handling

```python
from fastapi import HTTPException, Request
from fastapi.responses import JSONResponse

@router.get("/items/{id}")
async def get_item(id: int, db: DB):
    item = await db.get(Item, id)
    if item is None:
        raise HTTPException(status_code=404, detail="Item not found")
    return item

# Domain exceptions → HTTP in one place
class DomainError(Exception):
    status = 400
class NotFound(DomainError):
    status = 404
class Conflict(DomainError):
    status = 409

@app.exception_handler(DomainError)
async def domain_error_handler(request: Request, exc: DomainError):
    return JSONResponse(status_code=exc.status,
                        content={"type": type(exc).__name__, "detail": str(exc)})
```

Default error shapes:
- `HTTPException` → `{"detail": "..."}`
- Validation → **422** `{"detail": [{"loc": ["body", "email"], "msg": "...", "type": "..."}]}`

> [!tip] Consider RFC 9457 **Problem Details** (`application/problem+json`) for a consistent error contract. See [[REST API Design]].

## Related
- [[Python Exceptions]] · [[HTTP Status Codes]]
