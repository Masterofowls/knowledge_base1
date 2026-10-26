---
tags: [tutorial, powershell, testing]
aliases: [PowerShell tutorial, Pester tutorial]
created: 2026-10-26
level: intermediate
time: 1-2h
---
# Tutorial - PowerShell Log Cleanup with Pester

> [!abstract] What you'll build
> A reusable advanced function `Remove-OldLog` that finds log files older than N days, optionally compresses them before deleting, supports `-WhatIf`/`-Confirm`, outputs objects, and is covered by Pester tests. Then you'll schedule it.

**Requires:** PowerShell 7 (`winget install Microsoft.PowerShell`), Pester 5 (`Install-Module Pester -Scope CurrentUser -Force`). **Notes used:** [[PowerShell Functions and Parameters]], [[Pester Testing]].

## Step 1 — Project layout
```
LogTools/
├── LogTools.psm1
├── LogTools.psd1        (optional manifest)
└── tests/LogTools.Tests.ps1
```

## Step 2 — The function
```powershell
# LogTools.psm1
function Remove-OldLog {
    <#
    .SYNOPSIS
    Deletes (optionally archives) log files older than a given age.
    .EXAMPLE
    Remove-OldLog -Path C:\logs -OlderThanDays 30 -Archive -WhatIf
    #>
    [CmdletBinding(SupportsShouldProcess, ConfirmImpact = 'Medium')]
    param(
        [Parameter(Mandatory, ValueFromPipeline, ValueFromPipelineByPropertyName)]
        [Alias('FullName')]
        [ValidateScript({ Test-Path $_ -PathType Container })]
        [string[]]$Path,

        [ValidateRange(1, 3650)]
        [int]$OlderThanDays = 30,

        [string[]]$Include = @('*.log', '*.txt'),

        [switch]$Archive,

        [switch]$Recurse
    )
    begin {
        $cutoff = (Get-Date).AddDays(-$OlderThanDays)
        Write-Verbose "Cutoff: $cutoff"
    }
    process {
        foreach ($dir in $Path) {
            $files = Get-ChildItem -Path $dir -Include $Include -File -Recurse:$Recurse -ErrorAction Stop |
                Where-Object LastWriteTime -lt $cutoff

            foreach ($file in $files) {
                $action = if ($Archive) { 'Archive and delete' } else { 'Delete' }
                if ($PSCmdlet.ShouldProcess($file.FullName, $action)) {
                    if ($Archive) {
                        $zip = "$($file.FullName).zip"
                        Compress-Archive -Path $file.FullName -DestinationPath $zip -Force
                    }
                    Remove-Item -LiteralPath $file.FullName -Force
                    [pscustomobject]@{
                        Path      = $file.FullName
                        SizeKB    = [math]::Round($file.Length / 1KB, 1)
                        AgeDays   = [int]((Get-Date) - $file.LastWriteTime).TotalDays
                        Archived  = [bool]$Archive
                    }
                }
            }
        }
    }
}
Export-ModuleMember -Function Remove-OldLog
```

> [!check] Checkpoint
> ```powershell
> Import-Module ./LogTools.psm1 -Force
> New-Item -ItemType Directory -Force ./demo | Out-Null
> 1..5 | ForEach-Object {
>     $f = New-Item "./demo/app$_.log" -Force -Value "line $_"
>     $f.LastWriteTime = (Get-Date).AddDays(-10 * $_)
> }
> Remove-OldLog -Path ./demo -OlderThanDays 25 -WhatIf          # preview
> Remove-OldLog -Path ./demo -OlderThanDays 25 -Archive -Verbose | Format-Table
> Get-ChildItem ./demo
> ```

## Step 3 — Pester tests
```powershell
# tests/LogTools.Tests.ps1
BeforeAll {
    Import-Module "$PSScriptRoot/../LogTools.psm1" -Force
}

Describe 'Remove-OldLog' {
    BeforeEach {
        $dir = New-Item -ItemType Directory -Path (Join-Path $TestDrive ([guid]::NewGuid())) -Force
        function New-AgedFile($name, $days) {
            $f = New-Item -Path (Join-Path $dir $name) -ItemType File -Value 'x' -Force
            $f.LastWriteTime = (Get-Date).AddDays(-$days)
        }
        New-AgedFile 'old.log' 40
        New-AgedFile 'new.log' 2
        New-AgedFile 'old.csv' 40
    }

    It 'deletes only matching files older than the cutoff' {
        $result = Remove-OldLog -Path $dir -OlderThanDays 30 -Confirm:$false
        $result.Path | Should -HaveCount 1
        Join-Path $dir 'old.log' | Should -Not -Exist
        Join-Path $dir 'new.log' | Should -Exist
        Join-Path $dir 'old.csv' | Should -Exist          # not in -Include
    }

    It 'archives before deleting with -Archive' {
        Remove-OldLog -Path $dir -OlderThanDays 30 -Archive -Confirm:$false | Out-Null
        Join-Path $dir 'old.log.zip' | Should -Exist
    }

    It 'changes nothing with -WhatIf' {
        Remove-OldLog -Path $dir -OlderThanDays 30 -WhatIf
        Join-Path $dir 'old.log' | Should -Exist
    }

    It 'accepts directories from the pipeline' {
        $result = Get-Item $dir | Remove-OldLog -OlderThanDays 30 -Confirm:$false
        $result.Archived | Should -BeFalse
    }

    It 'rejects a non-existent path' {
        { Remove-OldLog -Path (Join-Path $TestDrive 'missing') } | Should -Throw
    }
}
```
```powershell
Invoke-Pester ./tests -Output Detailed
```
> [!check] Checkpoint — 5 tests pass. `$TestDrive` is wiped after the run, so nothing touches your real disk.

## Step 4 — Schedule it (Windows)
```powershell
$action  = New-ScheduledTaskAction -Execute 'pwsh.exe' -Argument '-NoProfile -Command "Import-Module C:\tools\LogTools; Remove-OldLog -Path C:\logs -OlderThanDays 30 -Archive -Confirm:$false"'
$trigger = New-ScheduledTaskTrigger -Daily -At 3am
Register-ScheduledTask -TaskName 'LogCleanup' -Action $action -Trigger $trigger -Description 'Archive and delete old logs'
```
On Linux/macOS, call `pwsh -Command ...` from cron.

## 🏋️ Challenges
1. Add `-MaxTotalSizeMB`: delete oldest files until the folder is under the limit.
2. Write a CSV report with `Export-Csv` and email/Teams-notify a summary via `Invoke-RestMethod` ([[PowerShell Web Requests]]).
3. Use `Mock Remove-Item` in a test to assert it's called with `-LiteralPath`.
4. Add a module manifest (`New-ModuleManifest`) and publish to a local repository.
5. Compare with the Bash version: [[Tutorial - Bash Backup Script]].

## Related
- [[Tutorials MOC]] · [[PowerShell MOC]] · [[PowerShell Error Handling]]
