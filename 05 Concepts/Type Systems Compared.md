---
tags: [concept, typescript, python, comparison]
aliases: [static vs dynamic typing, TS vs Python types]
created: 2026-10-26
status: evergreen
---
# Type Systems Compared

| | JavaScript | TypeScript | Python (+ hints) |
|---|---|---|---|
| Checking | dynamic, weak (coercion) | static (compile-time), erased at runtime | dynamic, strong; hints checked by mypy/pyright |
| Typing style | — | **structural** | nominal classes + structural `Protocol` |
| Null safety | `null`/`undefined` | `strictNullChecks` | `Optional[T]` / `T \| None` |
| Unions | — | `A \| B`, discriminated unions | `A \| B`, `Literal`, `match` |
| Generics | — | `<T>` | `TypeVar` / PEP 695 `[T]` |
| Runtime validation | manual | Zod/Valibot | Pydantic, attrs, dataclasses |
| Escape hatch | — | `any`, `as` | `Any`, `cast()`, `# type: ignore` |

**Strong vs weak:** Python raises on `"1" + 1`; JS returns `"11"`.
**Static vs dynamic:** TS/mypy catch errors before running; Python/JS catch them when the line executes.

> [!tip] In both ecosystems: types at the edges come from a **schema** ([[TS Runtime Validation]], [[FastAPI Pydantic Models]]) and ideally from the **API contract** ([[OpenAPI Schemas and Components]]).

## Related
- [[TS Basic Types]] · [[Python Type Hints]] · [[JS Data Types and Coercion]]
