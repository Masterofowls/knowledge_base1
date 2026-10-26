---
tags: [pytest, testing, concept]
aliases: [test discovery, running pytest]
created: 2026-10-26
status: evergreen
---
# pytest Basics

```python
# tests/test_cart.py
from app.cart import Cart

def test_empty_cart_total_is_zero():
    assert Cart().total() == 0

class TestDiscounts:                 # group tests; no __init__ needed
    def test_vip_gets_10_percent(self):
        cart = Cart(vip=True); cart.add(price=100)
        assert cart.total() == 90
```

## Discovery rules (defaults)
- Files: `test_*.py` or `*_test.py`
- Functions: `test_*`
- Classes: `Test*` (no `__init__`), methods `test_*`
- Starting from `testpaths` / rootdir

## Running
```bash
pytest                         # all
pytest tests/test_cart.py      # one file
pytest tests/test_cart.py::TestDiscounts::test_vip_gets_10_percent   # node id
pytest -k "discount and not slow"   # name expression
pytest -m smoke                # marker
pytest -x                      # stop at first failure
pytest --lf / --ff             # last-failed / failed-first
pytest -q / -v / -vv           # verbosity
pytest -s                      # don't capture print output
pytest --pdb                   # debugger on failure
pytest --durations=10          # slowest tests
```

## Layout
```
src/app/...
tests/
├── conftest.py        # shared fixtures (auto-discovered)
├── unit/
└── integration/
```

## Related
- [[pytest Configuration and CLI]] · [[Python Modules and Imports]] · [[Arrange-Act-Assert]]
