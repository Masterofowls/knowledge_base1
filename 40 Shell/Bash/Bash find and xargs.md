---
tags: [bash, snippet]
aliases: [find, xargs]
created: 2026-10-26
status: evergreen
---
# Bash find and xargs

```bash
find . -name "*.py" -not -path "./.venv/*"
find . -type f -size +100M
find . -type f -mtime -1                 # modified in last 24h
find . -name "__pycache__" -type d -prune -exec rm -rf {} +
find logs -name "*.log" -mtime +30 -delete
find . -type f -name "*.sh" -exec chmod +x {} +

# Safe with spaces/newlines in names
find . -name "*.tmp" -print0 | xargs -0 rm -f
find src -name "*.ts" -print0 | xargs -0 grep -l "any"

# Parallel
find img -name "*.png" -print0 | xargs -0 -P 8 -n 1 optipng
```

> [!tip] `fd` is a friendlier `find`: `fd -e py`, `fd -H -t f config`.

## Related
- [[Bash grep sed awk]] · [[PowerShell File System]] (`Get-ChildItem -Recurse`)
