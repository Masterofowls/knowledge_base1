---
tags: [powershell, concept]
aliases: [PowerShell variables, hashtable, arrays, string interpolation]
created: 2026-10-26
status: evergreen
---
# PowerShell Variables and Types

```powershell
$name = 'Ada'                       # single quotes: literal
"Hello $name"                       # double quotes: interpolate
"Files: $($files.Count)"            # subexpression for properties/expressions
[int]$port = '8080'                 # type constraint / cast
$env:API_URL = 'http://localhost'   # environment variable (this session)

# Arrays
$xs = @(1, 2, 3)
$xs += 4                             # creates a new array (slow in loops)
$list = [System.Collections.Generic.List[string]]::new(); $list.Add('a')
$xs[0]; $xs[-1]; $xs[1..2]

# Hashtables
$cfg = @{ Host = 'localhost'; Port = 5432 }
$cfg.Host; $cfg['Port']; $cfg.Keys
$ordered = [ordered]@{ a = 1; b = 2 }

# Custom objects
$user = [pscustomobject]@{ Name = 'Ada'; Role = 'admin' }

# Here-strings
$json = @'
{ "literal": "$notExpanded" }
'@
```

## Comparison operators (not `==`, `<`!)
`-eq -ne -gt -ge -lt -le` · `-like 'a*'` (wildcard) · `-match '^\d+$'` (regex, sets `$Matches`) · `-contains` / `-in` · `-is [int]` · case-sensitive variants: `-ceq`, `-cmatch`

```powershell
$null -eq $value        # put $null on the LEFT (arrays compare element-wise otherwise)
```

Automatic variables: `$_`, `$?`, `$LASTEXITCODE` (native exe exit code), `$PSScriptRoot`, `$args`, `$true`/`$false`/`$null`, `$Error`.

## Related
- [[Bash Variables and Quoting]] · [[PowerShell Control Flow]]
