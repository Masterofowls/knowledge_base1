---
tags: [playwright, pytest, python, testing, snippet]
aliases: [pytest-playwright]
created: 2026-10-26
status: evergreen
---
# Playwright Python

```bash
uv add --dev pytest-playwright
uv run playwright install --with-deps chromium
uv run pytest --browser chromium --browser firefox --headed --base-url http://localhost:8000
uv run pytest --tracing retain-on-failure --video retain-on-failure
uv run playwright codegen localhost:8000 --target python-pytest
```

```python
import re
from playwright.sync_api import Page, APIRequestContext, expect

def test_login(page: Page):
    page.goto("/login")
    page.get_by_label("Email").fill("ada@example.com")
    page.get_by_label("Password").fill("secret123")
    page.get_by_role("button", name="Sign in").click()
    expect(page).to_have_url(re.compile(r"/dashboard"))
    expect(page.get_by_role("heading", level=1)).to_have_text("Dashboard")

def test_api_create(playwright):
    ctx: APIRequestContext = playwright.request.new_context(base_url="http://localhost:8000")
    res = ctx.post("/items", data={"name": "Pen", "price": 150})
    assert res.status == 201
    assert res.json()["name"] == "Pen"
    ctx.dispose()
```

Fixtures from the plugin: `page`, `context`, `browser`, `browser_name`, `playwright`; override `browser_context_args` for viewport, locale, `storage_state`.

Naming maps 1:1 from TS: `getByRole` → `get_by_role`, `toHaveText` → `to_have_text`.

## Related
- [[pytest Fixtures]] · [[Playwright Locators]] · [[Playwright Assertions]]
