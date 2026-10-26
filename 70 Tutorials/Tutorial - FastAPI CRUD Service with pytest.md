---
tags: [tutorial, fastapi, python, pytest, sql]
aliases: [FastAPI tutorial]
created: 2026-10-26
level: intermediate
time: 3h
---
# Tutorial - FastAPI CRUD Service with pytest

> [!abstract] What you'll build
> The same **Bookmarks API** as the [[Tutorial - REST API with Express and TypeScript|Express tutorial]], in FastAPI with SQLAlchemy 2.0 (SQLite), Pydantic schemas, dependency injection, and a pytest suite that isolates every test.

**Prerequisites:** Python 3.12+ and uv ([[Python Virtual Environments and Packaging]]). **Notes used:** [[FastAPI MOC]], [[pytest Fixtures]].

## Step 1 — Project
```bash
uv init bookmarks && cd bookmarks
uv add "fastapi[standard]" sqlalchemy
uv add --dev pytest
mkdir -p app tests && touch app/__init__.py tests/__init__.py
```

## Step 2 — Database and model
```python
# app/db.py
from collections.abc import Iterator
from sqlalchemy import create_engine
from sqlalchemy.orm import DeclarativeBase, Session, sessionmaker

DATABASE_URL = "sqlite:///./bookmarks.db"
engine = create_engine(DATABASE_URL, connect_args={"check_same_thread": False})
SessionLocal = sessionmaker(bind=engine, expire_on_commit=False)

class Base(DeclarativeBase):
    pass

def get_db() -> Iterator[Session]:
    with SessionLocal() as session:
        yield session
```
```python
# app/models.py
from datetime import datetime, timezone
from sqlalchemy import JSON, String
from sqlalchemy.orm import Mapped, mapped_column
from app.db import Base

class Bookmark(Base):
    __tablename__ = "bookmarks"
    id: Mapped[int] = mapped_column(primary_key=True)
    url: Mapped[str] = mapped_column(String(2048))
    title: Mapped[str] = mapped_column(String(200), index=True)
    tags: Mapped[list[str]] = mapped_column(JSON, default=list)
    created_at: Mapped[datetime] = mapped_column(default=lambda: datetime.now(timezone.utc))
```

## Step 3 — Schemas
```python
# app/schemas.py
from datetime import datetime
from pydantic import BaseModel, ConfigDict, Field, HttpUrl

class BookmarkCreate(BaseModel):
    url: HttpUrl
    title: str = Field(min_length=1, max_length=200)
    tags: list[str] = []

class BookmarkUpdate(BaseModel):
    url: HttpUrl | None = None
    title: str | None = Field(default=None, min_length=1, max_length=200)
    tags: list[str] | None = None

class BookmarkOut(BaseModel):
    model_config = ConfigDict(from_attributes=True)
    id: int
    url: str
    title: str
    tags: list[str]
    created_at: datetime
```

## Step 4 — Routes
```python
# app/main.py
from typing import Annotated
from fastapi import Depends, FastAPI, HTTPException, Query, Response, status
from sqlalchemy import select
from sqlalchemy.orm import Session

from app.db import Base, engine, get_db
from app.models import Bookmark
from app.schemas import BookmarkCreate, BookmarkOut, BookmarkUpdate

app = FastAPI(title="Bookmarks API")
Base.metadata.create_all(engine)          # use Alembic in real projects

DB = Annotated[Session, Depends(get_db)]

def get_or_404(db: Session, bookmark_id: int) -> Bookmark:
    bm = db.get(Bookmark, bookmark_id)
    if bm is None:
        raise HTTPException(status_code=404, detail="Bookmark not found")
    return bm

@app.get("/health")
def health():
    return {"status": "ok"}

@app.get("/bookmarks", response_model=list[BookmarkOut])
def list_bookmarks(db: DB, q: str | None = None, tag: str | None = None,
                   limit: Annotated[int, Query(ge=1, le=100)] = 20, offset: Annotated[int, Query(ge=0)] = 0):
    stmt = select(Bookmark).order_by(Bookmark.id)
    if q:
        stmt = stmt.where(Bookmark.title.ilike(f"%{q}%"))     # parameterized by SQLAlchemy
    items = db.scalars(stmt).all()
    if tag:
        items = [b for b in items if tag in b.tags]
    return items[offset: offset + limit]

@app.get("/bookmarks/{bookmark_id}", response_model=BookmarkOut)
def get_bookmark(bookmark_id: int, db: DB):
    return get_or_404(db, bookmark_id)

@app.post("/bookmarks", response_model=BookmarkOut, status_code=status.HTTP_201_CREATED)
def create_bookmark(data: BookmarkCreate, db: DB, response: Response):
    bm = Bookmark(url=str(data.url), title=data.title, tags=data.tags)
    db.add(bm)
    db.commit()
    db.refresh(bm)
    response.headers["Location"] = f"/bookmarks/{bm.id}"
    return bm

@app.patch("/bookmarks/{bookmark_id}", response_model=BookmarkOut)
def update_bookmark(bookmark_id: int, patch: BookmarkUpdate, db: DB):
    bm = get_or_404(db, bookmark_id)
    for field, value in patch.model_dump(exclude_unset=True).items():
        setattr(bm, field, str(value) if field == "url" else value)
    db.commit()
    db.refresh(bm)
    return bm

@app.delete("/bookmarks/{bookmark_id}", status_code=status.HTTP_204_NO_CONTENT)
def delete_bookmark(bookmark_id: int, db: DB):
    db.delete(get_or_404(db, bookmark_id))
    db.commit()
```
> [!info] Handlers are plain `def` because the SQLite driver is synchronous — FastAPI runs them in a threadpool ([[FastAPI Async and Concurrency]]).

> [!check] Checkpoint
> ```bash
> uv run fastapi dev app/main.py
> ```
> Open http://localhost:8000/docs, create a bookmark with **Try it out**, then:
> ```bash
> http :8000/bookmarks tag==python          # HTTPie
> curl -s localhost:8000/openapi.json | jq '.paths | keys'
> ```

## Step 5 — Test fixtures with an isolated database
```python
# tests/conftest.py
import pytest
from fastapi.testclient import TestClient
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy.pool import StaticPool

from app.db import Base, get_db
from app.main import app

@pytest.fixture
def client():
    engine = create_engine("sqlite://", connect_args={"check_same_thread": False}, poolclass=StaticPool)
    Base.metadata.create_all(engine)
    TestingSession = sessionmaker(bind=engine, expire_on_commit=False)

    def override_get_db():
        with TestingSession() as s:
            yield s

    app.dependency_overrides[get_db] = override_get_db
    with TestClient(app) as c:
        yield c
    app.dependency_overrides.clear()

@pytest.fixture
def make_bookmark(client):
    def _make(**overrides):
        body = {"url": "https://example.com", "title": "Example", "tags": ["demo"], **overrides}
        r = client.post("/bookmarks", json=body)
        assert r.status_code == 201, r.text
        return r.json()
    return _make
```
> [!info] Each test gets a brand-new in-memory database — no leftover rows, no order dependence ([[Test Isolation and Flakiness]]).

## Step 6 — Tests
```python
# tests/test_bookmarks.py
import pytest

def test_create(client):
    r = client.post("/bookmarks", json={"url": "https://fastapi.tiangolo.com", "title": "FastAPI"})
    assert r.status_code == 201
    body = r.json()
    assert body["title"] == "FastAPI" and body["tags"] == []
    assert r.headers["location"] == f"/bookmarks/{body['id']}"

@pytest.mark.parametrize("payload, bad_field", [
    ({"url": "not-a-url", "title": "x"}, "url"),
    ({"url": "https://a.io", "title": ""}, "title"),
    ({"title": "no url"}, "url"),
])
def test_validation(client, payload, bad_field):
    r = client.post("/bookmarks", json=payload)
    assert r.status_code == 422
    assert bad_field in [err["loc"][-1] for err in r.json()["detail"]]

def test_filter_by_tag_and_search(client, make_bookmark):
    make_bookmark(title="Python docs", tags=["python"])
    make_bookmark(title="Rust book", tags=["rust"])
    assert [b["title"] for b in client.get("/bookmarks", params={"tag": "python"}).json()] == ["Python docs"]
    assert len(client.get("/bookmarks", params={"q": "book"}).json()) == 1

def test_patch_only_changes_sent_fields(client, make_bookmark):
    bm = make_bookmark(tags=["keep"])
    r = client.patch(f"/bookmarks/{bm['id']}", json={"title": "New title"})
    assert r.json()["title"] == "New title"
    assert r.json()["tags"] == ["keep"]

def test_delete_then_404(client, make_bookmark):
    bm = make_bookmark()
    assert client.delete(f"/bookmarks/{bm['id']}").status_code == 204
    assert client.get(f"/bookmarks/{bm['id']}").status_code == 404

def test_pagination_bounds(client):
    assert client.get("/bookmarks", params={"limit": 0}).status_code == 422
```
```bash
uv run pytest -v
```

> [!check] Checkpoint — 8 tests pass (parametrize expands to 3). Run `uv run pytest -k validation -v` to see the generated test IDs.

## 🏋️ Challenges
1. Move the tag filter into SQL (hint: a separate `tags` table with a many-to-many relation — [[SQL Normalization]]).
2. Switch to async SQLAlchemy + `aiosqlite` and `async def` handlers ([[FastAPI Database with SQLAlchemy]]).
3. Add Alembic migrations and remove `create_all`.
4. Add JWT auth and per-user bookmarks ([[FastAPI Authentication]]).
5. Fuzz the API from its schema: `uvx schemathesis run http://localhost:8000/openapi.json` ([[OpenAPI Tooling]]).

> [!success]- Solution sketch: challenge 1
> ```python
> bookmark_tags = Table("bookmark_tags", Base.metadata,
>     Column("bookmark_id", ForeignKey("bookmarks.id", ondelete="CASCADE"), primary_key=True),
>     Column("tag_id", ForeignKey("tags.id"), primary_key=True))
>
> class Tag(Base):
>     __tablename__ = "tags"
>     id: Mapped[int] = mapped_column(primary_key=True)
>     name: Mapped[str] = mapped_column(String(50), unique=True)
>
> # in Bookmark: tags: Mapped[list[Tag]] = relationship(secondary=bookmark_tags, lazy="selectin")
> # query: select(Bookmark).join(Bookmark.tags).where(Tag.name == tag)
> ```

## Related
- [[Tutorials MOC]] · [[FastAPI Testing]] · [[Express vs FastAPI]]
