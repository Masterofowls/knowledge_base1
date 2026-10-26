---
tags: [powershell, snippet]
aliases: [advanced functions, CmdletBinding, param block]
created: 2026-10-26
status: evergreen
---
# PowerShell Functions and Parameters

```powershell
function Get-LargeFile {
    <#
    .SYNOPSIS
    Lists files larger than a threshold.
    .EXAMPLE
    Get-LargeFile -Path C:\logs -MinSizeMB 50
    #>
    [CmdletBinding(SupportsShouldProcess)]
    param(
        [Parameter(Mandatory, ValueFromPipeline)]
        [ValidateScript({ Test-Path $_ })]
        [string]$Path,

        [ValidateRange(1, 10000)]
        [int]$MinSizeMB = 100,

        [ValidateSet('Name', 'Size')]
        [string]$SortBy = 'Size',

        [switch]$Delete
    )
    process {
        $files = Get-ChildItem -Path $Path -Recurse -File |
            Where-Object Length -gt ($MinSizeMB * 1MB)
        foreach ($f in $files) {
            if ($Delete -and $PSCmdlet.ShouldProcess($f.FullName, 'Delete')) {
                Remove-Item $f.FullName
            }
            [pscustomobject]@{ Name = $f.FullName; SizeMB = [math]::Round($f.Length / 1MB, 1) }
        }
    }
}

'C:\logs', 'D:\data' | Get-LargeFile -MinSizeMB 50 -Verbose
Get-LargeFile -Path C:\tmp -Delete -WhatIf
```

Key ideas:
- `[CmdletBinding()]` gives `-Verbose`, `-Debug`, `-ErrorAction`, etc. for free.
- `SupportsShouldProcess` → `-WhatIf` / `-Confirm`.
- `begin {}` / `process {}` (per pipeline item) / `end {}` blocks.
- Every un-captured value is **output** — use `$null = ...` or `[void]` to suppress.
- Use `Write-Verbose`/`Write-Information`, not `Write-Host`, for logs.

## Related
- [[Bash Functions]] · [[PowerShell Error Handling]] · [[Pester Testing]]
