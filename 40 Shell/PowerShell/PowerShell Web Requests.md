---
tags: [powershell, http, snippet]
aliases: [Invoke-RestMethod, Invoke-WebRequest, irm, iwr]
created: 2026-10-26
status: evergreen
---
# PowerShell Web Requests

> [!summary] TL;DR
> `Invoke-RestMethod` (irm) parses JSON into objects automatically. `Invoke-WebRequest` (iwr) returns the full response (status, headers, raw content).

```powershell
# GET → objects
$repo = Invoke-RestMethod https://api.github.com/repos/PowerShell/PowerShell
$repo.stargazers_count

# POST JSON
$body = @{ email = 'ada@example.com'; name = 'Ada' } | ConvertTo-Json
$user = Invoke-RestMethod -Uri http://localhost:8000/users -Method Post `
          -Body $body -ContentType 'application/json'

# Headers / auth
$headers = @{ Authorization = "Bearer $env:API_TOKEN" }
Invoke-RestMethod -Uri $url -Headers $headers
Invoke-RestMethod -Uri $url -Authentication Bearer -Token (ConvertTo-SecureString $env:API_TOKEN -AsPlainText)

# Full response & status codes (PS 7)
$r = Invoke-WebRequest $url -SkipHttpErrorCheck
$r.StatusCode; $r.Headers['Content-Type']; $r.Content

# Errors
try { Invoke-RestMethod $url } catch { $_.Exception.Response.StatusCode.value__ }

# Download / upload
Invoke-WebRequest $fileUrl -OutFile .\file.zip
Invoke-RestMethod -Uri $url -Method Post -Form @{ file = Get-Item .\photo.jpg }
```

## Related
- [[curl in PowerShell]] · [[curl MOC]] · [[HTTPie MOC]]
- [[HTTP Methods]] · [[HTTP Headers]]
