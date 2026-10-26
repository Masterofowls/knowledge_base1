---
tags: [curl, powershell, snippet]
aliases: [curl.exe, curl alias PowerShell]
created: 2026-10-26
status: evergreen
---
# curl in PowerShell

> [!warning] The alias trap
> In **Windows PowerShell 5.1**, `curl` is an alias for `Invoke-WebRequest` — flags like `-H`, `-d`, `-X` don't work. PowerShell 7 removed the alias, but call **`curl.exe`** explicitly to be safe.

```powershell
curl.exe -s https://api.example.com/users

# Quoting JSON: PowerShell eats double quotes when passing to native exes (fixed in 7.3+)
curl.exe --json '{"name":"Pen","price":150}' http://localhost:8000/items          # PS 7.3+
curl.exe --json '{\"name\":\"Pen\",\"price\":150}' http://localhost:8000/items    # PS 5.1 / older 7.x

# Simplest: body from file
@{ name = 'Pen'; price = 150 } | ConvertTo-Json | Set-Content item.json
curl.exe --json "@item.json" http://localhost:8000/items     # quote @ — it's the splatting operator

# Line continuation uses backtick, not backslash
curl.exe -X POST http://localhost:8000/items `
  -H "Content-Type: application/json" `
  -d "@item.json"
```

Or just use the native cmdlets → [[PowerShell Web Requests]].

## Related
- [[curl MOC]] · [[Bash vs PowerShell Rosetta]]
