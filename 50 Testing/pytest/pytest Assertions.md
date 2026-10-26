---
tags: [pytest, testing, snippet]
aliases: [pytest.raises, pytest.approx, assert rewriting]
created: 2026-10-26
status: evergreen
---
# pytest Assertions

pytest rewrites plain `assert` to show detailed diffs — no `assertEqual` needed.

```python
import pytest

assert result == {"id": 1, "name": "Ada"}       # dict diff on failure
assert "error" not in response.text
assert len(items) == 3
assert user.is_active, "user should be active"   # custom message

# Floats
assert 0.1 + 0.2 == pytest.approx(0.3)
assert [0.1, 0.2] == pytest.approx([0.1, 0.2], rel=1e-3)

# Exceptions
with pytest.raises(ValueError, match=r"must be positive"):
    account.deposit(-5)

with pytest.raises(NotFound) as exc_info:
    repo.get(999)
assert exc_info.value.id == 999

# Exception groups (3.11+)
with pytest.RaisesGroup(ValueError): ...           # pytest 8.4+

# Warnings
with pytest.warns(DeprecationWarning):
    old_api()

# Explicit failure / skip
pytest.fail("should not get here")
pytest.skip("requires network")
```

> [!tip] Compare whole objects rather than many field-by-field asserts — the diff tells you everything at once.

## Related
- [[Python Exceptions]] · [[Jest Matchers]] — the Jest counterpart
