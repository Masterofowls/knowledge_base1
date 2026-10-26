---
tags: [powershell, snippet]
aliases: [Get-ChildItem, Test-Path, Join-Path, file operations]
created: 2026-10-26
status: evergreen
---
# PowerShell File System

```powershell
Get-ChildItem -Path . -Recurse -Filter *.py -File | Where-Object FullName -notmatch '\\.venv\\'
Get-ChildItem -Recurse -Directory -Filter node_modules | Remove-Item -Recurse -Force
Test-Path .\out; New-Item -ItemType Directory -Force .\out
Join-Path $PSScriptRoot 'data' 'users.csv'               # PS 7 accepts multiple children
Resolve-Path .\file.txt
Split-Path $path -Leaf / -Parent / -Extension (PS 7)

Get-Content app.log -Tail 50 -Wait                       # tail -f
Get-Content big.csv -TotalCount 10                       # head
(Get-Content .env) -replace 'localhost', 'db' | Set-Content .env    # sed -i
Set-Content out.txt 'hello' -Encoding utf8NoBOM
Add-Content log.txt "$(Get-Date -Format o) started"

Copy-Item .\src .\backup -Recurse
Get-ChildItem *.jpg | Rename-Item -NewName { $_.Name -replace '^IMG_', 'photo_' }
Compress-Archive -Path .\dist\* -DestinationPath release.zip
Expand-Archive release.zip -DestinationPath .\out

Get-FileHash .\setup.exe -Algorithm SHA256
(Get-ChildItem -Recurse -File | Measure-Object Length -Sum).Sum / 1GB
```

> [!warning] `>` / `Out-File` in Windows PowerShell 5.1 write **UTF-16LE**. Use `Set-Content -Encoding utf8` there; PS 7 defaults to UTF-8 (no BOM).

## Related
- [[Bash Navigation and Files]] · [[Bash find and xargs]]
