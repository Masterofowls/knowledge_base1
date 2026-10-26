---
tags: [fastapi, guide]
aliases: [Learn FastAPI, FastAPI roadmap]
created: 2026-10-26
---
# FastAPI Getting Started

## 1. Install
```bash
uv init myapi && cd myapi
uv add "fastapi[standard]"
```

## 2. Hello world
```python
# main.py
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class Greeting(BaseModel):
    name: str

@app.get("/")
def root():
    return {"message": "Hello FastAPI"}

@app.post("/greet")
def greet(body: Greeting):
    return {"message": f"Hello, {body.name}!"}
```
```bash
uv run fastapi dev main.py        # http://localhost:8000/docs
```
Try `POST /greet` in the Swagger UI, then send `{}` and read the 422 error.

## 3. Learning checklist
- [ ] App, uvicorn, layout → [[FastAPI Basics]]
- [ ] Path/query/body params → [[FastAPI Routing and Parameters]]
- [ ] Pydantic models → [[FastAPI Pydantic Models]]
- [ ] Dependency injection → [[FastAPI Dependency Injection]]
- [ ] Errors → [[FastAPI Error Handling]]
- [ ] `async def` vs `def` → [[FastAPI Async and Concurrency]]
- [ ] Database with SQLAlchemy → [[FastAPI Database with SQLAlchemy]]
- [ ] Auth (OAuth2/JWT) → [[FastAPI Authentication]]
- [ ] Testing → [[FastAPI Testing]]
- [ ] OpenAPI customisation → [[FastAPI and OpenAPI]]
- [ ] Practise: [[Tutorial - FastAPI CRUD Service with pytest]]

## 4. Beginner mistakes
- Blocking calls (`requests`, `time.sleep`, sync DB) inside `async def`.
- One Pydantic model for create/update/response → leaking passwords or requiring IDs on create.
- Route order: `/users/{id}` declared before `/users/me`.
- Creating DB sessions globally instead of per request via `Depends`.
- `create_all()` in production instead of Alembic migrations.

## Resources
- fastapi.tiangolo.com — the tutorial is excellent; read "Tutorial - User Guide" in order
- docs.pydantic.dev — Pydantic v2
- docs.sqlalchemy.org — "Unified Tutorial" (2.0 style)
