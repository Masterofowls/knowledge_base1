---
tags: [pytest, testing, concept]
aliases: [fixtures, conftest.py, fixture scope, yield fixture]
created: 2026-10-26
status: evergreen
---
# pytest Fixtures

> [!summary] TL;DR
> A fixture is a function that provides test data/resources. Tests **request fixtures by parameter name**; pytest builds the dependency graph, caches per **scope**, and runs teardown after `yield`.

```python
# tests/conftest.py — fixtures here are visible to all tests below this folder
import pytest

@pytest.fixture
def user():
    return User(name="Ada", email="ada@example.com")

@pytest.fixture(scope="session")
def engine():
    eng = create_engine(TEST_DB_URL)
    Base.metadata.create_all(eng)
    yield eng                           # ← test runs here
    Base.metadata.drop_all(eng)         # teardown

@pytest.fixture
def db_session(engine):                 # fixtures can use fixtures
    conn = engine.connect()
    tx = conn.begin()
    session = Session(bind=conn)
    yield session
    session.close(); tx.rollback(); conn.close()   # isolate each test

@pytest.fixture
def make_user(db_session):              # factory fixture
    def _make(**overrides):
        u = User(**{"name": "Ada", "email": f"{uuid4()}@x.io", **overrides})
        db_session.add(u); db_session.flush()
        return u
    return _make

def test_admin_can_delete(make_user):
    admin = make_user(role="admin")
    assert admin.can("delete")
```

## Scopes
`function` (default) · `class` · `module` · `package` · `session` — wider scope = faster but more shared state.

## Other features
- `autouse=True` — applies to every test in scope (use sparingly).
- `@pytest.fixture(params=["sqlite", "postgres"])` — runs dependent tests per param (`request.param`).
- `@pytest.mark.usefixtures("clean_env")` — use without receiving the value.
- `request.addfinalizer(fn)` — alternative teardown.
- `pytest --fixtures` — list available fixtures; `--setup-show` — trace setup/teardown.

## Related
- [[pytest Built-in Fixtures]] · [[Test Isolation and Flakiness]]
- [[FastAPI Dependency Injection]] — same DI idea · [[Playwright Fixtures]] — same idea in Playwright
- [[Python Generators and Iterators]] · [[Python Context Managers]]
