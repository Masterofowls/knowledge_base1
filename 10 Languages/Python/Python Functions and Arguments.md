---
tags: [python, concept]
aliases: [args kwargs, keyword-only, scope LEGB]
created: 2026-10-26
status: evergreen
---
# Python Functions and Arguments

```python
def connect(host, port=5432, /, *args, timeout=10, **kwargs):
    ...
#          ^positional-only  ^extra pos  ^keyword-only  ^extra kw

def create_user(name: str, *, admin: bool = False) -> User:   # admin MUST be passed by name
    ...
create_user("ada", admin=True)

def log(*args, **kwargs):
    print(args, kwargs)
log(1, 2, level="info")     # (1, 2) {'level': 'info'}

params = {"admin": True}
create_user("ada", **params)    # unpack into call
```

## Scope: LEGB
**L**ocal → **E**nclosing → **G**lobal → **B**uiltin.
```python
def counter():
    count = 0
    def inc():
        nonlocal count      # rebind enclosing variable
        count += 1
        return count
    return inc
```

## First-class functions
```python
sorted(users, key=lambda u: (u.last, u.first))
from functools import partial
int_from_hex = partial(int, base=16)
```

## Related
- [[Python Decorators]]
- [[JS Functions and Closures]] · [[JS Variables and Scope]]
