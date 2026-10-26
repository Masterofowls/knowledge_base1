---
tags: [python, concept]
aliases: [typing, mypy, type annotations]
created: 2026-10-26
status: evergreen
---
# Python Type Hints

> [!summary] TL;DR
> Type hints are **not enforced at runtime** by Python itself. Static checkers (mypy, pyright) check them; libraries (Pydantic, FastAPI) *read* them at runtime to validate.

```python
from typing import Literal, TypedDict, Callable, Any, Self
from collections.abc import Iterable, Sequence

def total(prices: Iterable[float], discount: float | None = None) -> float: ...

Status = Literal["active", "banned"]
type UserId = int                         # 3.12 type alias

class UserDict(TypedDict):
    id: int
    name: str

Handler = Callable[[Request], Response]

# Generics (3.12 syntax)
def first[T](xs: Sequence[T]) -> T | None:
    return xs[0] if xs else None

class Stack[T]:
    def __init__(self) -> None: self.items: list[T] = []
    def push(self, x: T) -> Self: self.items.append(x); return self
```

## Narrowing
```python
def show(x: int | str | None):
    if x is None: return "-"
    if isinstance(x, int): return f"{x:,}"
    return x.upper()

match event:                             # structural pattern matching
    case {"type": "click", "x": x, "y": y}: ...
    case Point(x=0, y=0): ...
    case _: ...
```

## Tooling
```bash
uv add --dev mypy        # or pyright / basedpyright
mypy --strict src/
```

## Related
- [[Type Systems Compared]]
- [[TS Basic Types]] · [[TS Generics]] · [[TS Unions and Narrowing]]
- [[FastAPI Pydantic Models]]
