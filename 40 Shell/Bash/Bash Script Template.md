---
tags: [bash, snippet]
aliases: [strict mode, set -euo pipefail, getopts, trap]
created: 2026-10-26
status: evergreen
---
# Bash Script Template

```bash
#!/usr/bin/env bash
# deploy.sh — deploy the app to an environment
set -Eeuo pipefail          # exit on error, unset vars, pipeline failures; ERR trap inherits
IFS=$'\n\t'

readonly SCRIPT_DIR="$(cd -- "$(dirname -- "${BASH_SOURCE[0]}")" && pwd)"

usage() {
  cat <<EOF
Usage: $(basename "$0") [-e env] [-n] [-v] <version>
  -e  environment (default: staging)
  -n  dry run
  -v  verbose
EOF
}

log() { printf '[%s] %s\n' "$(date +%T)" "$*" >&2; }
cleanup() { rm -rf "${tmpdir:-}"; }
trap cleanup EXIT
trap 'log "failed at line $LINENO: $BASH_COMMAND"' ERR

env="staging"; dry_run=false
while getopts ":e:nvh" opt; do
  case "$opt" in
    e) env="$OPTARG" ;;
    n) dry_run=true ;;
    v) set -x ;;
    h) usage; exit 0 ;;
    \?) log "unknown option -$OPTARG"; usage; exit 2 ;;
    :)  log "-$OPTARG needs a value"; exit 2 ;;
  esac
done
shift $((OPTIND - 1))
version="${1:?$(usage)}"

tmpdir="$(mktemp -d)"
log "Deploying $version to $env (dry run: $dry_run)"
$dry_run || ./scripts/release.sh "$env" "$version"
```

> [!tip] Lint every script with **ShellCheck** (`shellcheck deploy.sh`) and format with `shfmt`.

> [!warning] `set -e` has surprising exceptions (inside `if`, `&&` chains, subshells in some contexts). It's a safety net, not error handling.

## Related
- [[Bash Functions]] · [[PowerShell Error Handling]]
