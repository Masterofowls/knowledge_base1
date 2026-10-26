---
tags: [pytest, testing, snippet]
aliases: [pytest-cov, pytest-xdist, pytest-asyncio, pytest-django]
created: 2026-10-26
status: evergreen
---
# pytest Plugins

| Plugin | Purpose | Usage |
|---|---|---|
| **pytest-cov** | coverage | `--cov=app --cov-report=term-missing` |
| **pytest-xdist** | parallel workers | `-n auto` |
| **pytest-mock** | `mocker` fixture | [[pytest Mocking]] |
| **pytest-asyncio** / **anyio** | async tests | [[pytest Async Testing]] |
| **pytest-django** | Django fixtures | [[Django Testing]] |
| **pytest-playwright** | browser fixtures | [[Playwright Python]] |
| **pytest-randomly** | random order + seeds | catches order deps |
| **pytest-rerunfailures** | `--reruns 2` | flaky mitigation (fix the root cause!) |
| **pytest-timeout** | `--timeout=30` | kill hung tests |
| **pytest-repeat** | `--count=50` | reproduce flakes |
| **pytest-benchmark** | micro-benchmarks | `benchmark(fn)` fixture |
| **pytest-env** | set env vars in config | |
| **respx** / **responses** | mock httpx / requests | |
| **freezegun** / **time-machine** | freeze time | |
| **factory_boy** / **polyfactory** | test data factories | |
| **hypothesis** | property-based testing | `@given(st.integers())` |
| **syrupy** | snapshot testing | like [[Jest Snapshot Testing]] |
| **schemathesis** | OpenAPI-driven fuzzing | [[OpenAPI Tooling]] |

```python
from hypothesis import given, strategies as st

@given(st.text())
def test_slugify_is_idempotent(s):
    assert slugify(slugify(s)) == slugify(s)
```

## Related
- [[pytest Configuration and CLI]] · [[Code Coverage]] · [[Test Isolation and Flakiness]]
