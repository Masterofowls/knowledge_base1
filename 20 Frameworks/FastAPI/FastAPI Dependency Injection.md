---
tags: [fastapi, concept]
aliases: [Depends, dependencies, DI]
created: 2026-10-26
status: evergreen
---
# FastAPI Dependency Injection

> [!summary] TL;DR
> `Depends(fn)` tells FastAPI to call `fn` (resolving *its* params too) and inject the result. Dependencies are cached per request, can `yield` for cleanup, and can be **overridden in tests**.

```python
from typing import Annotated
from fastapi import Depends, HTTPException, Query

# yield dependency → setup/teardown per request
async def get_db():
    async with SessionLocal() as session:
        yield session

DB = Annotated[AsyncSession, Depends(get_db)]

# class/func dependency for shared query params
class Pagination:
    def __init__(self, limit: int = Query(20, le=100), offset: int = 0):
        self.limit, self.offset = limit, offset

# dependency chain
async def get_current_user(token: Annotated[str, Depends(oauth2_scheme)], db: DB) -> User:
    user = await users.from_token(db, token)
    if not user:
        raise HTTPException(401, "Invalid token", headers={"WWW-Authenticate": "Bearer"})
    return user

CurrentUser = Annotated[User, Depends(get_current_user)]

def require_admin(user: CurrentUser) -> User:
    if not user.is_admin:
        raise HTTPException(403, "Admins only")
    return user

@router.get("/orders")
async def list_orders(db: DB, user: CurrentUser, page: Annotated[Pagination, Depends()]):
    return await orders.for_user(db, user.id, page.limit, page.offset)

@router.delete("/users/{id}", dependencies=[Depends(require_admin)])
async def delete_user(id: int, db: DB): ...
```

> [!tip] Dependencies are the FastAPI answer to Django middleware/decorators *and* to pytest fixtures — the mental model is the same as [[pytest Fixtures]].

## Related
- [[FastAPI Authentication]] · [[FastAPI Testing]] (overrides)
- [[Python Context Managers]]
