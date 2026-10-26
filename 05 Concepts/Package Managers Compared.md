---
tags: [concept, javascript, python, comparison]
aliases: [npm vs pip, npm vs uv, lockfiles]
created: 2026-10-26
status: evergreen
---
# Package Managers Compared

| Task | npm | pnpm | pip + venv | uv |
|---|---|---|---|---|
| manifest | `package.json` | `package.json` | `requirements.txt` / `pyproject.toml` | `pyproject.toml` |
| lockfile | `package-lock.json` | `pnpm-lock.yaml` | (pip-tools `requirements.lock`) | `uv.lock` |
| new project | `npm init -y` | `pnpm init` | `python -m venv .venv` | `uv init` |
| add dep | `npm i zod` | `pnpm add zod` | `pip install pydantic` | `uv add pydantic` |
| add dev dep | `npm i -D jest` | `pnpm add -D jest` | (separate file) | `uv add --dev pytest` |
| install from lock | `npm ci` | `pnpm i --frozen-lockfile` | `pip install -r requirements.txt` | `uv sync --locked` |
| run script / tool | `npm run test` / `npx tsc` | `pnpm test` / `pnpm dlx` | activate venv, run | `uv run pytest` / `uvx ruff` |
| isolation | `node_modules/` per project | content-addressed store + symlinks | `.venv/` | `.venv/` managed for you |
| upgrade | `npm update` / `npm outdated` | `pnpm up` | `pip install -U` | `uv lock --upgrade` |
| audit | `npm audit` | `pnpm audit` | `pip-audit` | `uvx pip-audit` |

> [!tip] Always commit lockfiles for applications; use `npm ci` / `uv sync --locked` in CI for reproducible builds.

## Related
- [[Python Virtual Environments and Packaging]] · [[JS Modules]]
