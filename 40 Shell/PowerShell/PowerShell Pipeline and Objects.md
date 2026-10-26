---
tags: [powershell, concept]
aliases: [Where-Object, Select-Object, ForEach-Object, Sort-Object, pipeline]
created: 2026-10-26
status: evergreen
---
# PowerShell Pipeline and Objects

> [!summary] TL;DR
> The pipeline passes **.NET objects**, not text. You filter and shape by property name — no `awk`/`cut` parsing needed.

```powershell
# Top 5 processes by memory
Get-Process |
  Sort-Object WorkingSet64 -Descending |
  Select-Object -First 5 Name, Id, @{ n = 'MB'; e = { [math]::Round($_.WorkingSet64 / 1MB) } }

# Filter
Get-Service | Where-Object Status -eq 'Running'
Get-ChildItem -Recurse -File | Where-Object { $_.Length -gt 100MB -and $_.Extension -eq '.log' }

# Transform / act on each
Get-ChildItem *.txt | ForEach-Object { $_.Name.ToUpper() }
1..5 | ForEach-Object -Parallel { Start-Sleep 1; $_ } -ThrottleLimit 5   # PS 7

# Group & measure
Get-ChildItem -File -Recurse | Group-Object Extension | Sort-Object Count -Descending
Get-ChildItem -File | Measure-Object Length -Sum -Average

# Text search (grep-like)
Select-String -Path .\logs\*.log -Pattern 'ERROR' | Select-Object Filename, LineNumber, Line
```

`$_` / `$PSItem` = the current pipeline object.

## Formatting & export — always LAST
```powershell
... | Format-Table -AutoSize      # display only! returns formatting objects
... | Export-Csv report.csv -NoTypeInformation
... | ConvertTo-Json -Depth 5
Get-Content data.json -Raw | ConvertFrom-Json
Import-Csv users.csv | Where-Object Role -eq 'admin'
... | Out-GridView                # GUI table (Windows)
```

> [!warning] Never pipe `Format-*` output into other commands — it's no longer your data.

## Related
- [[Bash Redirection and Pipes]] · [[Bash grep sed awk]] · [[jq Cheat Sheet]]
