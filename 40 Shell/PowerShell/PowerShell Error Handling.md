---
tags: [powershell, concept]
aliases: [try catch PowerShell, ErrorAction, terminating errors]
created: 2026-10-26
status: evergreen
---
# PowerShell Error Handling

> [!summary] TL;DR
> PowerShell has **non-terminating** errors (logged, execution continues) and **terminating** errors (stop, catchable). `try/catch` only catches terminating ones — so add `-ErrorAction Stop`.

```powershell
$ErrorActionPreference = 'Stop'          # script-wide: make errors terminating
Set-StrictMode -Version Latest           # catch typos / uninitialised vars

try {
    $data = Get-Content .\config.json -Raw -ErrorAction Stop | ConvertFrom-Json
    Invoke-RestMethod $data.url -TimeoutSec 10
}
catch [System.IO.FileNotFoundException] {
    Write-Warning "Config missing: $($_.Exception.Message)"
}
catch {
    Write-Error "Unexpected: $_"
    throw                                 # rethrow
}
finally {
    Write-Verbose 'done'
}

throw [System.ArgumentException]::new('Bad input')
```

## Native executables
`try/catch` does **not** catch non-zero exit codes of `git`, `npm`, `python`… Check `$LASTEXITCODE`:
```powershell
npm run build
if ($LASTEXITCODE -ne 0) { throw "build failed ($LASTEXITCODE)" }
# PS 7.3+: $PSNativeCommandUseErrorActionPreference = $true
```

## Related
- [[Bash Script Template]] — `set -euo pipefail` equivalent
- [[Python Exceptions]] · [[JS Error Handling]]
