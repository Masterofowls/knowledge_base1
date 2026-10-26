---
tags: [fastapi, sql, snippet]
aliases: [SQLAlchemy, Alembic, SQLModel]
created: 2026-10-26
status: evergreen
---
# FastAPI Database with SQLAlchemy

```python
# db.py — SQLAlchemy 2.0 async
from sqlalchemy.ext.asyncio import create_async_engine, async_sessionmaker, AsyncSession
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, relationship
from sqlalchemy import ForeignKey, String, select

engine = create_async_engine(settings.database_url, pool_pre_ping=True)
SessionLocal = async_sessionmaker(engine, expire_on_commit=False)

class Base(DeclarativeBase): pass

class User(Base):
    __tablename__ = "users"
    id: Mapped[int] = mapped_column(primary_key=True)
    email: Mapped[str] = mapped_column(String(255), unique=True, index=True)
    orders: Mapped[list["Order"]] = relationship(back_populates="user")

class Order(Base):
    __tablename__ = "orders"
    id: Mapped[int] = mapped_column(primary_key=True)
    user_id: Mapped[int] = mapped_column(ForeignKey("users.id", ondelete="CASCADE"))
    total: Mapped[int]
    user: Mapped[User] = relationship(back_populates="orders")
```

```python
# router
from sqlalchemy.orm import selectinload

@router.get("/users/{id}", response_model=UserOut)
async def get_user(id: int, db: DB):
    user = await db.scalar(select(User).where(User.id == id).options(selectinload(User.orders)))
    if not user:
        raise HTTPException(404)
    return user

@router.post("/users", response_model=UserOut, status_code=201)
async def create_user(data: UserCreate, db: DB):
    user = User(email=data.email)
    db.add(user)
    await db.commit()
    await db.refresh(user)
    return user
```

## Migrations with Alembic
```bash
alembic init -t async migrations
alembic revision --autogenerate -m "create users"
alembic upgrade head
alembic downgrade -1
```

> [!warning] Async SQLAlchemy forbids lazy loading — use `selectinload`/`joinedload` explicitly (like Django's `prefetch_related`/`select_related`).

## Related
- [[SQL MOC]] · [[SQL Joins]] · [[SQL Transactions and Isolation]]
- [[Django Models and ORM]] · [[Django QuerySets and Performance]]
