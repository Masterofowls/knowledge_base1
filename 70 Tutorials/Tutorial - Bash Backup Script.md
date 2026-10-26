---
tags: [tutorial, bash]
aliases: [Bash tutorial, backup script tutorial]
created: 2026-10-26
level: beginner-intermediate
time: 1-2h
---
# Tutorial - Bash Backup Script

> [!abstract] What you'll build
> `backup.sh` — compresses a directory into a timestamped archive, keeps the last N backups, logs what it does, supports `--dry-run`, and fails safely. Along the way you'll use strict mode, functions, argument parsing, `find`, traps and ShellCheck.

**Runs on:** Linux, macOS, WSL, or Git Bash on Windows. **Notes used:** [[Bash Script Template]], [[Bash Functions]], [[Bash find and xargs]].

## Step 1 — Skeleton with strict mode
```bash
#!/usr/bin/env bash
# backup.sh — archive a directory and rotate old backups
set -Eeuo pipefail

log() { printf '%s [%s] %s\n' "$(date '+%F %T')" "$1" "${*:2}" >&2; }
die() { log ERROR "$*"; exit 1; }

log INFO "starting"
```
```bash
chmod +x backup.sh && ./backup.sh
```

## Step 2 — Arguments
```bash
usage() {
  cat <<EOF
Usage: $(basename "$0") [-k keep] [-d dest] [-n] <source_dir>
  -k  number of backups to keep (default 7)
  -d  destination directory (default ./backups)
  -n  dry run: show what would happen
EOF
}

keep=7; dest="./backups"; dry_run=false
while getopts ":k:d:nh" opt; do
  case "$opt" in
    k) keep="$OPTARG" ;;
    d) dest="$OPTARG" ;;
    n) dry_run=true ;;
    h) usage; exit 0 ;;
    \?) usage; die "unknown option -$OPTARG" ;;
    :)  die "option -$OPTARG needs a value" ;;
  esac
done
shift $((OPTIND - 1))
[[ $# -eq 1 ]] || { usage; exit 2; }
src="$1"

[[ -d "$src" ]] || die "source '$src' is not a directory"
[[ "$keep" =~ ^[0-9]+$ && "$keep" -ge 1 ]] || die "-k must be a positive integer"
```

> [!check] Checkpoint — `./backup.sh`, `./backup.sh -k abc docs`, `./backup.sh -h`: each prints the right message and exit code (`echo $?`).

## Step 3 — Create the archive
```bash
run() {                         # execute or just print, depending on dry-run
  if $dry_run; then log DRY "$*"; else "$@"; fi
}

name="$(basename "$(realpath "$src")")"
stamp="$(date +%Y%m%d-%H%M%S)"
archive="$dest/${name}-${stamp}.tar.gz"

run mkdir -p "$dest"
tmp="$(mktemp "${TMPDIR:-/tmp}/backup.XXXXXX")"
trap 'rm -f "$tmp"' EXIT                       # clean up even on failure

log INFO "archiving $src -> $archive"
run tar -czf "$tmp" -C "$(dirname "$(realpath "$src")")" "$name" \
    --exclude='node_modules' --exclude='.venv' --exclude='*.tmp'
run mv "$tmp" "$archive"                       # atomic-ish: never leave a half-written archive
$dry_run || log INFO "size: $(du -h "$archive" | cut -f1)"
```
> [!info] Writing to a temp file and moving it at the end means a crash never leaves a corrupt "latest" backup.

## Step 4 — Rotate old backups
```bash
rotate() {
  local pattern="${name}-*.tar.gz"
  mapfile -t backups < <(find "$dest" -maxdepth 1 -name "$pattern" -type f | sort -r)
  local count=${#backups[@]}
  if (( count <= keep )); then
    log INFO "$count backups, nothing to rotate"
    return
  fi
  for old in "${backups[@]:keep}"; do
    log INFO "removing $old"
    run rm -f -- "$old"
  done
}
rotate
log INFO "done"
```
> [!check] Checkpoint
> ```bash
> mkdir -p demo && echo hi > demo/a.txt
> for i in 1 2 3 4; do ./backup.sh -k 2 demo; sleep 1; done
> ls backups/                       # only 2 archives remain
> ./backup.sh -n -k 1 demo          # dry run: shows actions, changes nothing
> tar -tzf backups/demo-*.tar.gz | head
> ```

## Step 5 — Lint and harden
```bash
shellcheck backup.sh              # install: apt/brew/scoop install shellcheck
```
Fix every warning. Then think about failure modes: disk full? source changes during backup? two runs at once? Add a lock:
```bash
exec 9>"$dest/.backup.lock"
flock -n 9 || die "another backup is running"
```

## Step 6 — Schedule it
```bash
crontab -e
# every day at 02:30, append logs
30 2 * * * /home/me/bin/backup.sh -k 14 -d /mnt/backups /home/me/projects >> /var/log/backup.log 2>&1
```
On Windows use Task Scheduler — or see the PowerShell version in [[Tutorial - PowerShell Log Cleanup with Pester]].

## 🏋️ Challenges
1. Add `-e` to encrypt the archive with `gpg --symmetric` (read the passphrase from an env var, not an argument).
2. Upload to S3 with `aws s3 cp` and verify with a checksum (`sha256sum`).
3. Send a failure notification with `curl` to a webhook from an `ERR` trap ([[curl Scripting Patterns]]).
4. Write tests with **bats-core**: `@test "fails on missing dir" { run ./backup.sh nope; [ "$status" -eq 1 ]; }`.
5. Port the script to PowerShell using [[Bash vs PowerShell Rosetta]].

## Related
- [[Tutorials MOC]] · [[Bash MOC]] · [[Bash Processes and Jobs]]
