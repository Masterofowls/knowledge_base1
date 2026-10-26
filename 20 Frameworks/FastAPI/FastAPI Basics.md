---
tags: [fastapi, concept]
aliases: [uvicorn, APIRouter, FastAPI app]
created: 2026-10-26
status: evergreen
---
# FastAPI Basics

```python
# app/main.py
from contextlib import asynccontextmanager
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from app.routers import users, items

@asynccontextmanager
async def lifespan(app: FastAPI):
    await db.connect()          # startup
    yield
    await db.disconnect()       # shutdown

app = FastAPI(title="Shop API", version="1.0.0", lifespan=lifespan)
app.add_middleware(CORSMiddleware, allow_origins=["http://localhost:5173"],
                   allow_methods=["*"], allow_headers=["*"], allow_credentials=True)
app.include_router(users.router, prefix="/users", tags=["users"])
app.include_router(items.router, prefix="/items", tags=["items"])

@app.get("/health")
def health():
    return {"status": "ok"}
```

```bash
uv add "fastapi[standard]"
uv run fastapi dev app/main.py           # dev server with reload
uv run uvicorn app.main:app --host 0.0.0.0 --port 8000 --workers 4   # prod-ish
```

Free docs: **`/docs`** (Swagger UI), **`/redoc`**, **`/openapi.json`**.

## Layout
```
app/
├── main.py
├── config.py          # pydantic-settings
├── db.py
├── models.py          # SQLAlchemy ORM
├── schemas.py         # Pydantic
├── deps.py            # shared dependencies
└── routers/{users,items}.py
tests/
```

## Related
- [[FastAPI Routing and Parameters]] · [[CORS]]
- [[Django Project Structure]]
