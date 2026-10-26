---
tags: [powershell, snippet]
aliases: [PowerShell profile, Install-Module, PSGallery]
created: 2026-10-26
status: evergreen
---
# PowerShell Modules and Profile

```powershell
Find-Module -Name *Docker*
Install-Module PSReadLine, posh-git, Pester -Scope CurrentUser
Import-Module posh-git
Get-Module -ListAvailable
Get-Command -Module Pester
# PS 7.4+: Install-PSResource is the newer equivalent
```

## Profile — your `.bashrc`
```powershell
$PROFILE                         # path to current-user profile
if (-not (Test-Path $PROFILE)) { New-Item -ItemType File -Path $PROFILE -Force }
notepad $PROFILE
```

```powershell
# Example profile content
Import-Module posh-git
Set-PSReadLineOption -PredictionSource History -EditMode Windows
Set-PSReadLineKeyHandler -Key Tab -Function MenuComplete
function gs { git status }
function .. { Set-Location .. }
Set-Alias -Name k -Value kubectl
```

## Writing a module
`MyTools.psm1` with functions + `Export-ModuleMember -Function Get-LargeFile`; optional manifest via `New-ModuleManifest`.

## Related
- [[PowerShell Functions and Parameters]] · [[Pester Testing]]
