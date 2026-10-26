---
tags: [bash, powershell, comparison]
aliases: [bash to powershell, powershell to bash]
created: 2026-10-26
status: evergreen
---
# Bash vs PowerShell Rosetta

| Task | Bash | PowerShell |
|---|---|---|
| list files | `ls -la` | `Get-ChildItem -Force` |
| recursive find | `find . -name "*.py"` | `Get-ChildItem -Recurse -Filter *.py` |
| read file | `cat f` | `Get-Content f` |
| tail -f | `tail -f app.log` | `Get-Content app.log -Wait -Tail 10` |
| grep | `grep -rn ERROR .` | `Select-String -Path * -Pattern ERROR` (or `sls`) |
| replace in file | `sed -i 's/a/b/g' f` | `(Get-Content f) -replace 'a','b' \| Set-Content f` |
| count lines | `wc -l f` | `(Get-Content f).Count` |
| set var | `x=1` | `$x = 1` |
| env var | `export X=1` / `$X` | `$env:X = 1` / `$env:X` |
| last exit code | `$?` (number, 0 = ok) | `$?` (bool) / `$LASTEXITCODE` (number, native exe) |
| if file exists | `[[ -f f ]]` | `Test-Path f -PathType Leaf` |
| string compare | `[[ $a == $b ]]` | `$a -eq $b` |
| loop | `for f in *.txt; do ...; done` | `foreach ($f in gci *.txt) { ... }` |
| function | `f() { echo "$1"; }` | `function f($a) { $a }` |
| pipe columns | `ps aux \| awk '{print $2}'` | `Get-Process \| Select-Object Id` |
| JSON | `curl ... \| jq .name` | `(irm ...).name` |
| HTTP POST | `curl -X POST -d @b.json -H 'Content-Type: application/json' url` | `irm url -Method Post -Body $json -ContentType application/json` |
| which | `which node` | `(Get-Command node).Source` |
| null device | `/dev/null` | `$null` (`> $null`) |
| background | `cmd &` | `Start-Job { cmd }` / `cmd &` (PS 7) |
| strict mode | `set -euo pipefail` | `$ErrorActionPreference='Stop'; Set-StrictMode -Version Latest` |
| line continuation | `\` | `` ` `` (backtick) or end line with `\|` |
| comment | `#` | `#` / `<# block #>` |

> [!tip] Core difference: Bash pipes **text** you parse; PowerShell pipes **objects** you query by property.

## Related
- [[Bash MOC]] · [[PowerShell MOC]] · [[curl in PowerShell]]
