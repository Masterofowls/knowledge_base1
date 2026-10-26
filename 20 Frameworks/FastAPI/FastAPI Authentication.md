---
tags: [fastapi, concept]
aliases: [OAuth2PasswordBearer, JWT fastapi]
created: 2026-10-26
status: growing
---
# FastAPI Authentication

```python
from datetime import datetime, timedelta, timezone
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
import jwt                                   # PyJWT
from pwdlib import PasswordHash              # argon2 hashing

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/auth/token")
hasher = PasswordHash.recommended()

def create_access_token(sub: str, minutes: int = 15) -> str:
    payload = {"sub": sub, "exp": datetime.now(timezone.utc) + timedelta(minutes=minutes)}
    return jwt.encode(payload, settings.jwt_secret, algorithm="HS256")

@router.post("/auth/token")
async def login(form: Annotated[OAuth2PasswordRequestForm, Depends()], db: DB):
    user = await users.by_email(db, form.username)
    if not user or not hasher.verify(form.password, user.password_hash):
        raise HTTPException(401, "Incorrect credentials", headers={"WWW-Authenticate": "Bearer"})
    return {"access_token": create_access_token(str(user.id)), "token_type": "bearer"}

async def get_current_user(token: Annotated[str, Depends(oauth2_scheme)], db: DB) -> User:
    try:
        payload = jwt.decode(token, settings.jwt_secret, algorithms=["HS256"])
    except jwt.InvalidTokenError:
        raise HTTPException(401, "Invalid token", headers={"WWW-Authenticate": "Bearer"})
    user = await db.get(User, int(payload["sub"]))
    if user is None:
        raise HTTPException(401, "User not found")
    return user
```

The "Authorize" button in `/docs` works automatically because `OAuth2PasswordBearer` registers a security scheme in OpenAPI ([[OpenAPI Security Schemes]]).

> [!warning] Short-lived access tokens + refresh tokens; never put secrets in JWT payloads (they're only base64-encoded); always pin `algorithms=[...]` when decoding.

## Related
- [[Authentication Schemes]] · [[FastAPI Dependency Injection]] · [[Django Authentication]]
