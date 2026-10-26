---
tags: [fastapi, openapi, snippet]
aliases: [fastapi docs, swagger fastapi]
created: 2026-10-26
status: evergreen
---
# FastAPI and OpenAPI

FastAPI generates an [[OpenAPI MOC|OpenAPI 3.1]] document from routes + Pydantic models + type hints.

```python
@router.post(
    "/items",
    response_model=ItemOut,
    status_code=201,
    summary="Create an item",
    description="Creates an item owned by the current user.",
    responses={409: {"model": ErrorOut, "description": "Duplicate SKU"}},
    tags=["items"],
    operation_id="createItem",          # nicer generated client method names
)
async def create_item(item: ItemCreate): ...

class ItemCreate(BaseModel):
    model_config = {"json_schema_extra": {"examples": [{"name": "Pen", "price": 150}]}}
    name: str
    price: int = Field(gt=0, description="Price in cents")
```

```bash
curl -s localhost:8000/openapi.json | jq '.paths | keys'
# Generate a typed TS client for the frontend
npx @hey-api/openapi-ts -i http://localhost:8000/openapi.json -o src/client
```

Hide a route: `include_in_schema=False`. Disable docs in prod: `FastAPI(docs_url=None, redoc_url=None)`.

## Related
- [[OpenAPI Tooling]] · [[OpenAPI Design-First vs Code-First]] · [[curl MOC]]
