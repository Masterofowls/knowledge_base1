---
tags: [python, concept]
aliases: [list dict set tuple, mutability]
created: 2026-10-26
status: evergreen
---
# Python Data Types and Collections

| Type | Literal | Mutable | Ordered | Notes |
|---|---|---|---|---|
| `list` | `[1, 2]` | ✅ | ✅ | dynamic array |
| `tuple` | `(1, 2)` | ❌ | ✅ | hashable if contents are |
| `dict` | `{"a": 1}` | ✅ | ✅ (insertion) | hash map |
| `set` | `{1, 2}` | ✅ | ❌ | unique, O(1) membership |
| `frozenset` | `frozenset({1})` | ❌ | ❌ | hashable set |
| `str` / `bytes` | `"x"` / `b"x"` | ❌ | ✅ | |

```python
nums = [3, 1, 2]
nums.sort()                 # in place, returns None
sorted(nums, reverse=True)  # new list

d = {"a": 1}
d.get("b", 0)               # default instead of KeyError
d |= {"b": 2}               # merge (3.9+)
{**d, "c": 3}

first, *rest = [1, 2, 3]    # unpacking
a, b = b, a                 # swap

from collections import Counter, defaultdict, deque, namedtuple
Counter("banana").most_common(1)    # [('a', 3)]
graph = defaultdict(list); graph["a"].append("b")
q = deque(maxlen=3)                 # O(1) appends/pops both ends
```

> [!warning] Mutable default arguments
> ```python
> def add(item, bucket=[]):   # ❌ shared between calls
> def add(item, bucket=None): # ✅
>     bucket = bucket if bucket is not None else []
> ```

> [!warning] `is` vs `==` — `is` checks identity; use it only for `None`, `True`, `False`, sentinels.

## Related
- [[Python Comprehensions]]
- [[JS Data Types and Coercion]] · [[JS Destructuring and Spread]]
