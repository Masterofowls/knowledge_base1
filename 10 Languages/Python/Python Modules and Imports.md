---
tags: [python, concept]
aliases: [imports, packages, __init__, __name__ main]
created: 2026-10-26
status: evergreen
---
# Python Modules and Imports

- **Module** = a `.py` file. **Package** = a directory of modules (usually with `__init__.py`).
- Imports are cached in `sys.modules` — module code runs **once**.

```python
import json
from pathlib import Path
from .models import User          # relative (inside a package)
from app.services import billing  # absolute (preferred)

if __name__ == "__main__":        # only when run as a script, not when imported
    main()
```

## src layout (recommended)
```
myproject/
├── pyproject.toml
├── src/myapp/__init__.py
├── src/myapp/core.py
└── tests/test_core.py
```
Run modules with `python -m myapp.core` so imports resolve from the package root.

> [!warning] Circular imports → `ImportError: cannot import name ... (most likely due to a circular import)`. Fix by moving shared code to a third module, or importing inside the function.

> [!warning] Naming a file `json.py`, `random.py`, `test.py` shadows the stdlib.

## Related
- [[Python Virtual Environments and Packaging]]
- [[JS Modules]]
- [[pytest Basics]] — rootdir and import modes
