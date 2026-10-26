---
tags: [bash, snippet]
aliases: [bash if, test command, double brackets, bash case, exit codes]
created: 2026-10-26
status: evergreen
---
# Bash Conditionals and Tests

> [!summary] TL;DR
> `if` tests a command's **exit code**: `0` = success = true. `[[ ... ]]` is Bash's safer test command.

```bash
if [[ -f "$file" && -r "$file" ]]; then
  echo "readable file"
elif [[ -d "$file" ]]; then
  echo "directory"
else
  echo "missing" >&2
  exit 1
fi

if grep -q "ERROR" app.log; then echo "found errors"; fi    # any command works
if ! command -v jq >/dev/null; then echo "install jq"; fi

[[ -z "$var" ]] && echo "empty"          # short-circuit style
mkdir -p out || exit 1
```

## Test operators
| Files | Strings | Numbers (or use `(( ))`) |
|---|---|---|
| `-e` exists | `-z` empty | `-eq` `-ne` |
| `-f` regular file | `-n` non-empty | `-lt` `-le` |
| `-d` directory | `==` / `!=` | `-gt` `-ge` |
| `-r -w -x` perms | `=~` regex | `(( a > b ))` |
| `-s` non-empty file | `<` `>` sort order | |

```bash
if [[ "$email" =~ ^[^@]+@[^@]+\.[a-z]+$ ]]; then echo valid; fi
if (( count > 10 )); then echo many; fi
```

## case
```bash
case "$1" in
  start|up)   start_app ;;
  stop)       stop_app ;;
  *.tar.gz)   tar xzf "$1" ;;
  *)          echo "usage: $0 {start|stop}"; exit 2 ;;
esac
```

## Related
- [[Bash Loops]] · [[PowerShell Control Flow]]
