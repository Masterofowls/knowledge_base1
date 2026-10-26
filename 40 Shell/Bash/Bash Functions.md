---
tags: [bash, snippet]
aliases: [bash functions, local, return]
created: 2026-10-26
status: evergreen
---
# Bash Functions

```bash
log() {
  local level="$1"; shift
  printf '%s [%s] %s\n' "$(date +%T)" "$level" "$*" >&2
}

die() { log ERROR "$*"; exit 1; }

is_port_open() {
  local host="$1" port="$2"
  (echo > "/dev/tcp/$host/$port") 2>/dev/null     # return code = last command's
}

get_version() {
  local v
  v=$(git describe --tags 2>/dev/null) || v="dev"
  echo "$v"                                        # "return" data via stdout
}

version=$(get_version)
is_port_open localhost 5432 || die "Postgres not running"
```

Key points:
- Arguments: `$1`, `$@`, `$#` (same as scripts).
- `return N` sets exit status (0–255) — not for data.
- Use `local` or variables leak globally.
- Output data on stdout; logs on stderr (`>&2`).

## Related
- [[Bash Script Template]] · [[PowerShell Functions and Parameters]]
