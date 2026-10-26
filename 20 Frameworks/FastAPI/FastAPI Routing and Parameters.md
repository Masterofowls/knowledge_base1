---
tags: [fastapi, snippet]
aliases: [path params, query params, request body, Annotated]
created: 2026-10-26
status: evergreen
---
# FastAPI Routing and Parameters

> [!summary] TL;DR
> FastAPI decides where a parameter comes from by its declaration: **in the path template → path param; simple type → query; Pydantic model → JSON body**. Use `Annotated[...]` with `Path`, `Query`, `Body`, `Header`, `Cookie` for validation & docs.

```python
from typing import Annotated
from fastapi import APIRouter, Path, Query, Header, status

router = APIRouter()

@router.get("/{item_id}")
async def get_item(
    item_id: Annotated[int, Path(ge=1)],
    q: Annotated[str | None, Query(max_length=50)] = None,
    tags: Annotated[list[str], Query()] = [],          # ?tags=a&tags=b
    limit: Annotated[int, Query(ge=1, le=100)] = 20,
    x_request_id: Annotated[str | None, Header()] = None,
):
    return {"id": item_id, "q": q, "tags": tags}

@router.post("/", status_code=status.HTTP_201_CREATED, response_model=ItemOut)
async def create_item(item: ItemCreate):                 # JSON body
    return await repo.create(item)

@router.patch("/{item_id}", response_model=ItemOut)
async def update_item(item_id: int, patch: ItemUpdate):
    data = patch.model_dump(exclude_unset=True)          # only fields sent
    return await repo.update(item_id, data)

@router.delete("/{item_id}", status_code=204)
async def delete_item(item_id: int) -> None: ...
```

> [!warning] Route order matters: declare `/users/me` **before** `/users/{user_id}`.

Invalid input → automatic **422 Unprocessable Entity** with details.

## Related
- [[FastAPI Pydantic Models]] · [[HTTP Methods]] · [[HTTP Status Codes]]
- [[OpenAPI Paths and Operations]]
