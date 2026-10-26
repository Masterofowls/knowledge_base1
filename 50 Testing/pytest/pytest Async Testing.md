---
tags: [pytest, testing, snippet]
aliases: [pytest-asyncio, async tests python]
created: 2026-10-26
status: evergreen
---
# pytest Async Testing

```python
# pip install pytest-asyncio ; asyncio_mode = "auto" in config → no marker needed
import pytest, pytest_asyncio, httpx

@pytest_asyncio.fixture
async def client():
    async with httpx.AsyncClient(transport=httpx.ASGITransport(app=app), base_url="http://t") as c:
        yield c

async def test_create_and_fetch(client):
    r = await client.post("/items", json={"name": "Pen", "price": 1})
    assert r.status_code == 201
    r2 = await client.get(f"/items/{r.json()['id']}")
    assert r2.json()["name"] == "Pen"

async def test_timeout_raises(mocker):
    mocker.patch("app.client.fetch", side_effect=asyncio.TimeoutError)
    with pytest.raises(ServiceUnavailable):
        await get_quote()
```

Alternative: AnyIO's plugin (ships with FastAPI/Starlette): `@pytest.mark.anyio` + an `anyio_backend` fixture returning `"asyncio"`.

> [!warning] Event-loop scope: session-scoped async fixtures need a matching loop scope (`loop_scope="session"` in pytest-asyncio ≥0.24).

## Related
- [[Python asyncio]] · [[FastAPI Testing]] · [[Jest Async Testing]]
