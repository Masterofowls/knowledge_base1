---
tags: [python, snippet]
aliases: [venv, pip, uv, pyproject.toml, poetry]
created: 2026-10-26
status: evergreen
---
# Python Virtual Environments and Packaging

> [!summary] TL;DR
> Always isolate project dependencies in a **virtual environment**. Declare them in `pyproject.toml`; lock them. Modern default: **uv** (fast, all-in-one).

## Plain venv + pip
```bash
python -m venv .venv
source .venv/bin/activate          # Windows PowerShell: .venv\Scripts\Activate.ps1
python -m pip install -U pip
pip install -r requirements.txt
pip freeze > requirements.txt
```

## uv (recommended)
```bash
uv init myapp && cd myapp
uv add fastapi "sqlalchemy>=2"     # updates pyproject + uv.lock
uv add --dev pytest ruff mypy
uv run pytest                      # runs inside the managed venv
uv sync                            # install exactly what's locked
uv python install 3.13
uvx ruff check .                   # run a tool ad-hoc (like npx)
```

## pyproject.toml
```toml
[project]
name = "myapp"
version = "0.1.0"
requires-python = ">=3.12"
dependencies = ["fastapi>=0.115", "sqlalchemy>=2.0"]

[dependency-groups]
dev = ["pytest>=8", "ruff", "mypy"]

[tool.pytest.ini_options]
testpaths = ["tests"]
addopts = "-ra --strict-markers"

[tool.ruff]
line-length = 100
```

> [!warning] PowerShell may block `Activate.ps1`: `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`. See [[PowerShell Basics and Cmdlets]].

## Related
- [[Package Managers Compared]]
- [[Python Modules and Imports]]
- [[pytest Configuration and CLI]]
