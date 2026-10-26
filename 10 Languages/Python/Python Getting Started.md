---
tags: [python, guide]
aliases: [Learn Python, Python roadmap]
created: 2026-10-26
---
# Python Getting Started

## 1. Install
```powershell
winget install astral-sh.uv          # Windows
```
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh     # macOS/Linux
uv python install 3.13
uv run python --version
```
Editor: VS Code + Python + Ruff extensions (or PyCharm).

## 2. Hello world
```bash
uv init hello && cd hello
```
```python
# main.py
def greet(name: str = "world") -> str:
    return f"Hello, {name}!"

if __name__ == "__main__":
    import sys
    print(greet(*sys.argv[1:2]))
```
```bash
uv run main.py Ada
uv run python            # REPL (3.13 has a much nicer one)
```

## 3. Learning checklist
- [ ] Collections & mutability → [[Python Data Types and Collections]]
- [ ] Comprehensions → [[Python Comprehensions]]
- [ ] Functions & arguments → [[Python Functions and Arguments]]
- [ ] Exceptions → [[Python Exceptions]]
- [ ] Modules & imports → [[Python Modules and Imports]]
- [ ] Classes & dataclasses → [[Python Classes and Dataclasses]]
- [ ] Decorators → [[Python Decorators]]
- [ ] Generators → [[Python Generators and Iterators]]
- [ ] Context managers → [[Python Context Managers]]
- [ ] Type hints → [[Python Type Hints]]
- [ ] asyncio → [[Python asyncio]]
- [ ] Packaging with uv → [[Python Virtual Environments and Packaging]]
- [ ] Practise: [[Tutorial - Python CLI Tool with TDD]]

## 4. Beginner mistakes
- Installing packages globally instead of in a venv.
- Mutable default arguments (`def f(x=[])`).
- Bare `except:`; catching `Exception` and ignoring it.
- Naming files after stdlib modules (`random.py`, `json.py`).
- `is` vs `==` confusion.

## 5. Next steps
[[pytest Getting Started]] → [[FastAPI Getting Started]] or [[Django Getting Started]].

## Resources
- Official tutorial — docs.python.org/3/tutorial
- Real Python — realpython.com
- "Fluent Python" (Ramalho) — for the intermediate leap
