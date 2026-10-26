---
tags: [powershell, snippet]
aliases: [PowerShell if, foreach, switch]
created: 2026-10-26
status: evergreen
---
# PowerShell Control Flow

```powershell
if (Test-Path $file) {
    'exists'
} elseif ($count -gt 10) {
    'many'
} else {
    'nope'
}

foreach ($f in Get-ChildItem *.log) { Compress-Archive $f "$($f.BaseName).zip" }
for ($i = 0; $i -lt 5; $i++) { $i }
while (-not (Test-NetConnection db -Port 5432 -InformationLevel Quiet)) { Start-Sleep 1 }
do { $n++ } until ($n -ge 3)

switch -Regex ($line) {
    '^ERROR'  { $errors++ }
    '^WARN'   { $warnings++ }
    default   { }
}

switch ($env) { 'prod' { $url = 'https://api' } 'dev' { $url = 'http://localhost' } }

# Ternary, null-coalescing, pipeline chains (PS 7+)
$label = $ok ? 'pass' : 'fail'
$port = $env:PORT ?? 8080
npm test && npm run build || Write-Error 'failed'
```

## Related
- [[Bash Conditionals and Tests]] · [[Bash Loops]]
