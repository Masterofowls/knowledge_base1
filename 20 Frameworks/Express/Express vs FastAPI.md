---
tags: [express, fastapi, comparison]
aliases: [Express or FastAPI, Node vs Python API]
created: 2026-10-26
status: evergreen
---
# Express vs FastAPI

| | Express | FastAPI |
|---|---|---|
| Language | JavaScript / TypeScript | Python |
| Core idea | middleware pipeline | typed functions + dependency injection |
| Validation | bring your own (Zod) | built-in via Pydantic |
| OpenAPI docs | manual / generators (zod-openapi, tsoa) | automatic `/docs` |
| Async | Node event loop, everything async | `async def` or threadpool `def` |
| Per-route concerns | middleware chain | `Depends()` |
| Errors | error middleware | exception handlers / `HTTPException` |
| Testing | [[Supertest MOC\|Supertest]] + [[Jest MOC\|Jest]] | `TestClient` + [[pytest MOC\|pytest]] |
| ORM | Prisma, Drizzle, pg | SQLAlchemy, SQLModel |
| Sweet spot | full-stack JS teams, BFFs, realtime (Socket.IO) | Python teams, data/ML services, typed APIs |

Same endpoint, both ways:

```ts
router.get("/items/:id", async (req, res) => {
  const item = await items.get(Number(req.params.id));
  if (!item) return res.status(404).json({ detail: "Not found" });
  res.json(item);
});
```

```python
@router.get("/items/{item_id}", response_model=ItemOut)
async def get_item(item_id: int, db: DB):
    item = await db.get(Item, item_id)
    if not item:
        raise HTTPException(404, "Not found")
    return item
```

## Related
- [[Express MOC]] · [[FastAPI MOC]] · [[Django vs FastAPI]]
