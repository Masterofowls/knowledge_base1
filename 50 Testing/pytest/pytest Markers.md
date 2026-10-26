---
tags: [pytest, testing, snippet]
aliases: [skip, xfail, custom markers, pytest.mark]
created: 2026-10-26
status: evergreen
---
# pytest Markers

```python
import sys, pytest

@pytest.mark.skip(reason="feature flag off")
def test_new_checkout(): ...

@pytest.mark.skipif(sys.platform == "win32", reason="POSIX only")
def test_symlinks(): ...

@pytest.mark.xfail(reason="upstream bug", strict=True)   # strict: XPASS = failure
def test_known_bug(): ...

@pytest.mark.slow
@pytest.mark.integration
def test_full_sync(): ...

pytestmark = pytest.mark.integration      # module-level: marks every test in file
```

Register custom markers (required with `--strict-markers`):
```toml
[tool.pytest.ini_options]
markers = [
  "slow: long-running tests",
  "integration: needs real services",
  "smoke: critical path subset",
]
```

```bash
pytest -m "not slow"
pytest -m "smoke or integration"
pytest --markers
pytest -rxs          # report reasons for skips/xfails
```

## Related
- [[pytest Configuration and CLI]] · [[Playwright CI and Parallelism]] (tags with `@smoke` grep)
