---
tags: [fastapi, testing, snippet]
aliases: [TestClient, dependency_overrides, httpx AsyncClient]
created: 2026-10-26
status: evergreen
---
# FastAPI Testing

```python
# tests/conftest.py
import pytest
from fastapi.testclient import TestClient
from app.main import app
from app.deps import get_db, get_current_user

@pytest.fixture
def client(db_session):
    app.dependency_overrides[get_db] = lambda: db_session
    with TestClient(app) as c:              # `with` runs lifespan events
        yield c
    app.dependency_overrides.clear()

@pytest.fixture
def auth_client(client, user):
    app.dependency_overrides[get_current_user] = lambda: user
    return client
```

```python
# tests/test_items.py
def test_create_item(auth_client):
    r = auth_client.post("/items/", json={"name": "Pen", "price": 150})
    assert r.status_code == 201
    body = r.json()
    assert body["name"] == "Pen" and "id" in body

def test_validation_error(auth_client):
    r = auth_client.post("/items/", json={"name": ""})
    assert r.status_code == 422
    assert r.json()["detail"][0]["loc"][-1] in {"name", "price"}

def test_requires_auth(client):
    assert client.get("/orders").status_code == 401
```

## Async tests
```python
import pytest
from httpx import AsyncClient, ASGITransport

@pytest.mark.anyio
async def test_health():
    async with AsyncClient(transport=ASGITransport(app=app), base_url="http://test") as ac:
        r = await ac.get("/health")
    assert r.json() == {"status": "ok"}
```

> [!tip] Contract-test the generated schema with **Schemathesis**: `schemathesis run http://localhost:8000/openapi.json`. See [[OpenAPI Tooling]].

## Related
- [[pytest Fixtures]] · [[pytest MOC]] · [[Supertest MOC]] — the Node equivalent
- [[FastAPI Dependency Injection]]
