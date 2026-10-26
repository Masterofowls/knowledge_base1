---
tags: [pytest, guide]
aliases: [Learn pytest, pytest roadmap]
created: 2026-10-26
---
# pytest Getting Started

## 1. Install
```bash
uv add --dev pytest pytest-cov
```

## 2. First test
```python
# src/calc.py
def divide(a: float, b: float) -> float:
    if b == 0:
        raise ZeroDivisionError("b must not be zero")
    return a / b

# tests/test_calc.py
import pytest
from calc import divide

def test_divide():
    assert divide(10, 4) == 2.5

def test_divide_by_zero():
    with pytest.raises(ZeroDivisionError, match="zero"):
        divide(1, 0)
```
```toml
# pyproject.toml
[tool.pytest.ini_options]
testpaths = ["tests"]
pythonpath = ["src"]
```
```bash
uv run pytest -v
uv run pytest --cov=src --cov-report=term-missing
```

## 3. Learning checklist
- [ ] Discovery & running → [[pytest Basics]]
- [ ] Assertions, `raises`, `approx` → [[pytest Assertions]]
- [ ] Fixtures & conftest → [[pytest Fixtures]]
- [ ] Built-in fixtures (`tmp_path`, `capsys`, `monkeypatch`) → [[pytest Built-in Fixtures]]
- [ ] Parametrize → [[pytest Parametrize]]
- [ ] Markers → [[pytest Markers]]
- [ ] Mocking → [[pytest Mocking]]
- [ ] Config & CLI → [[pytest Configuration and CLI]]
- [ ] Plugins → [[pytest Plugins]]
- [ ] Async tests → [[pytest Async Testing]]
- [ ] Concepts: [[Arrange-Act-Assert]], [[Test Doubles]], [[Test-Driven Development]]
- [ ] Practise: [[Tutorial - Python CLI Tool with TDD]], [[Tutorial - FastAPI CRUD Service with pytest]]

## 4. Beginner mistakes
- Tests that depend on each other or on execution order.
- Patching where a function is *defined* instead of where it's *used*.
- Using `unittest`-style `self.assertEqual` (works, but you lose pytest's nicer diffs).
- Huge `autouse` fixtures nobody understands.
- Testing private implementation details instead of behaviour.

## Resources
- docs.pytest.org — "Get started" and "How-to guides"
- "Python Testing with pytest" (Brian Okken)
