---
tags: [fastapi, python, concept]
aliases: [Pydantic, BaseModel, schemas, DTO]
created: 2026-10-26
status: evergreen
---
# FastAPI Pydantic Models

> [!summary] TL;DR
> Pydantic v2 models parse & validate data from type hints. In FastAPI they define request bodies, response shapes (filtering out secret fields), and the [[OpenAPI Schemas and Components|OpenAPI schemas]].

```python
from datetime import datetime
from pydantic import BaseModel, EmailStr, Field, ConfigDict, field_validator, model_validator

class UserBase(BaseModel):
    email: EmailStr
    name: str = Field(min_length=1, max_length=100)

class UserCreate(UserBase):
    password: str = Field(min_length=8)

    @field_validator("name")
    @classmethod
    def strip_name(cls, v: str) -> str:
        return v.strip()

class UserUpdate(BaseModel):            # PATCH: everything optional
    email: EmailStr | None = None
    name: str | None = None

class UserOut(UserBase):                # never includes password
    model_config = ConfigDict(from_attributes=True)   # read from ORM objects
    id: int
    created_at: datetime

class DateRange(BaseModel):
    start: datetime
    end: datetime
    @model_validator(mode="after")
    def check(self):
        if self.end <= self.start:
            raise ValueError("end must be after start")
        return self
```

## Common API
```python
u = UserCreate.model_validate({"email": "a@b.co", "name": " Ada ", "password": "secret123"})
u.model_dump()                      # → dict
u.model_dump(exclude_unset=True)    # only provided fields (PATCH)
u.model_dump_json()
UserOut.model_json_schema()          # JSON Schema
```

## Settings
```python
from pydantic_settings import BaseSettings, SettingsConfigDict
class Settings(BaseSettings):
    model_config = SettingsConfigDict(env_file=".env")
    database_url: str
    jwt_secret: str
    debug: bool = False
settings = Settings()
```

> [!tip] Separate **Create / Update / Out** models — same pattern as [[TS Utility Types]] `Omit`/`Partial`.

## Related
- [[Python Classes and Dataclasses]] · [[Python Type Hints]]
- [[TS Runtime Validation]] — Zod is the TS counterpart
