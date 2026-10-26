---
tags: [powershell, concept]
aliases: [cmdlets, Get-Help, Get-Command, execution policy]
created: 2026-10-26
status: evergreen
---
# PowerShell Basics and Cmdlets

> [!summary] TL;DR
> Commands are **cmdlets** named `Verb-Noun` (`Get-Process`, `Set-Location`). Discover everything with three commands: `Get-Command`, `Get-Help`, `Get-Member`.

```powershell
Get-Command *process*            # find commands
Get-Command -Noun Item
Get-Help Get-ChildItem -Examples # (Update-Help once, as admin)
Get-Process | Get-Member         # what properties/methods do these objects have?
Get-Verb                         # approved verbs

$PSVersionTable.PSVersion        # which PowerShell?
```

## Common cmdlets
| Task | Cmdlet | Alias |
|---|---|---|
| list files | `Get-ChildItem` | `ls`, `dir`, `gci` |
| change dir | `Set-Location` | `cd` |
| read file | `Get-Content` | `cat`, `gc` |
| write file | `Set-Content` / `Out-File` / `Add-Content` | |
| copy/move/delete | `Copy-Item` / `Move-Item` / `Remove-Item` | `cp`/`mv`/`rm` |
| processes | `Get-Process` / `Stop-Process` | `ps` / `kill` |
| services | `Get-Service` / `Restart-Service` | |
| search text | `Select-String` | `sls` |
| HTTP | `Invoke-RestMethod` / `Invoke-WebRequest` | `irm` / `iwr` |
| env var | `$env:PATH` | |

## Parameters
```powershell
Get-ChildItem -Path C:\logs -Filter *.log -Recurse -File
Remove-Item .\build -Recurse -Force -WhatIf      # preview, don't do it
Stop-Process -Name node -Confirm                 # ask first
```
Parameter names can be abbreviated as long as unambiguous (`-Rec`), but spell them out in scripts.

## Execution policy (Windows)
```powershell
Get-ExecutionPolicy -List
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned   # allow local scripts
```

> [!tip] Use aliases interactively; **use full cmdlet names in scripts** for readability and portability (`ls`/`curl` mean different things on Linux).

## Related
- [[PowerShell Pipeline and Objects]] · [[Bash Navigation and Files]] · [[Bash vs PowerShell Rosetta]]
