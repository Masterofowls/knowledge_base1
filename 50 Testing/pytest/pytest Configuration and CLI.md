---
tags: [pytest, testing, snippet]
aliases: [pyproject pytest, pytest.ini, conftest hooks]
created: 2026-10-26
status: evergreen
---
# pytest Configuration and CLI

```toml
# pyproject.toml
[tool.pytest.ini_options]
minversion = "8.0"
testpaths = ["tests"]
pythonpath = ["src"]
addopts = [
  "-ra",                 # summary of all non-passing
  "--strict-markers",
  "--strict-config",
  "--import-mode=importlib",
]
markers = ["slow: slow tests", "integration: needs services"]
filterwarnings = ["error", "ignore::DeprecationWarning:third_party.*"]
asyncio_mode = "auto"    # if using pytest-asyncio
```

## Custom CLI option via conftest hook
```python
# conftest.py
def pytest_addoption(parser):
    parser.addoption("--base-url", default="http://localhost:8000")

@pytest.fixture(scope="session")
def base_url(request):
    return request.config.getoption("--base-url")

def pytest_collection_modifyitems(config, items):
    if not config.getoption("--run-slow", default=False):
        skip = pytest.mark.skip(reason="need --run-slow")
        for item in items:
            if "slow" in item.keywords:
                item.add_marker(skip)
```

## CI flags
```bash
pytest -n auto --dist loadscope \
  --junitxml=reports/junit.xml \
  --cov=app --cov-report=xml \
  -p no:cacheprovider
```

## Related
- [[pytest Basics]] · [[pytest Plugins]] · [[Python Virtual Environments and Packaging]]
