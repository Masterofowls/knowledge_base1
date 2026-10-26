---
tags: [pytest, testing, snippet]
aliases: [parametrize, data-driven tests]
created: 2026-10-26
status: evergreen
---
# pytest Parametrize

```python
import pytest

@pytest.mark.parametrize(
    ("raw", "expected"),
    [
        ("Hello World", "hello-world"),
        ("  trim  ", "trim"),
        ("Ünïcödé", "unicode"),
        pytest.param("", "", id="empty"),
        pytest.param("a/b", "a-b", marks=pytest.mark.xfail(reason="bug #42")),
    ],
)
def test_slugify(raw, expected):
    assert slugify(raw) == expected

# Stacked → cartesian product (2 × 3 = 6 tests)
@pytest.mark.parametrize("method", ["GET", "HEAD"])
@pytest.mark.parametrize("path", ["/", "/health", "/docs"])
def test_public_routes(client, method, path):
    assert client.request(method, path).status_code == 200

# Readable ids
@pytest.mark.parametrize("status", [400, 401, 403], ids=lambda s: f"http_{s}")
def test_error_pages(status): ...
```

Run a single case: `pytest "tests/test_slug.py::test_slugify[empty]"`.

> [!tip] Parametrize also works on fixtures (`@pytest.fixture(params=[...])`) — useful to run a whole suite against multiple backends.

## Related
- [[pytest Fixtures]] · [[Jest Basics]] (`test.each`) · [[Arrange-Act-Assert]]
