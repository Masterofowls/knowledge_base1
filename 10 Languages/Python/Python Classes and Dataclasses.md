---
tags: [python, concept]
aliases: [classes, dataclass, dunder methods, Protocol, OOP]
created: 2026-10-26
status: evergreen
---
# Python Classes and Dataclasses

```python
class Account:
    interest = 0.02                       # class attribute (shared)

    def __init__(self, owner: str, balance: float = 0):
        self.owner = owner                # instance attribute
        self._balance = balance           # "_" = internal by convention

    @property
    def balance(self) -> float:
        return self._balance

    def deposit(self, amount: float) -> None:
        if amount <= 0:
            raise ValueError("amount must be positive")
        self._balance += amount

    @classmethod
    def empty(cls, owner): return cls(owner, 0)

    @staticmethod
    def validate_iban(iban: str) -> bool: ...

    def __repr__(self): return f"Account({self.owner!r}, {self._balance})"
```

## Useful dunder methods
`__repr__`, `__str__`, `__eq__`, `__hash__`, `__lt__`, `__len__`, `__iter__`, `__getitem__`, `__contains__`, `__call__`, `__enter__`/`__exit__`.

## Dataclasses — boilerplate-free records
```python
from dataclasses import dataclass, field

@dataclass(frozen=True, slots=True)
class Point:
    x: float
    y: float
    tags: list[str] = field(default_factory=list)

Point(1, 2) == Point(1, 2)   # True — __eq__ generated
```

## Protocol — structural typing (duck typing, type-checked)
```python
from typing import Protocol
class SupportsClose(Protocol):
    def close(self) -> None: ...
def shutdown(x: SupportsClose): x.close()
```

## Related
- [[Python Type Hints]] · [[FastAPI Pydantic Models]] — Pydantic vs dataclasses
- [[JS Prototypes and Classes]] · [[TS Interfaces vs Type Aliases]]
