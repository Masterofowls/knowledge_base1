---
tags: [testing, concept]
aliases: [coverage, branch coverage]
created: 2026-10-26
status: evergreen
---
# Code Coverage

> [!summary] TL;DR
> Coverage tells you what code **ran** during tests, not whether it was **verified**. Useful to find untested areas; harmful as a target to game.

Types: **line/statement**, **branch** (both sides of each `if`), function, condition/MC-DC.

```bash
# Python
pytest --cov=app --cov-branch --cov-report=term-missing --cov-report=html
# Jest
npx jest --coverage
# Playwright (JS) — via V8 coverage + monocart/istanbul reporters
```

```toml
[tool.coverage.report]
fail_under = 85
exclude_lines = ["if TYPE_CHECKING:", "raise NotImplementedError"]
```

> [!tip] Better signals: branch coverage on changed lines (diff coverage), and **mutation testing** (mutmut, Stryker) — does a test fail if the code is broken?

## Related
- [[pytest Plugins]] · [[Jest Configuration]]
