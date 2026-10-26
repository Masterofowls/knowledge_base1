---
tags: [powershell, testing, snippet]
aliases: [Pester]
created: 2026-10-26
status: growing
---
# Pester Testing

PowerShell's test framework (v5).

```powershell
# Get-LargeFile.Tests.ps1
BeforeAll {
    . $PSScriptRoot/Get-LargeFile.ps1
}

Describe 'Get-LargeFile' {
    BeforeEach {
        $dir = New-Item -ItemType Directory -Path (Join-Path $TestDrive 'data')
        Set-Content (Join-Path $dir 'small.txt') 'x'
        $big = Join-Path $dir 'big.bin'
        [IO.File]::WriteAllBytes($big, [byte[]]::new(2MB))
    }

    It 'returns only files over the threshold' {
        $result = Get-LargeFile -Path $TestDrive -MinSizeMB 1
        $result | Should -HaveCount 1
        $result[0].Name | Should -BeLike '*big.bin'
    }

    It 'does not delete with -WhatIf' {
        Get-LargeFile -Path $TestDrive -MinSizeMB 1 -Delete -WhatIf
        Join-Path $TestDrive 'data/big.bin' | Should -Exist
    }

    Context 'with mocks' {
        It 'calls Remove-Item when -Delete' {
            Mock Remove-Item {}
            Get-LargeFile -Path $TestDrive -MinSizeMB 1 -Delete -Confirm:$false
            Should -Invoke Remove-Item -Times 1
        }
    }
}
```

```powershell
Invoke-Pester -Output Detailed
Invoke-Pester -CI            # NUnit XML + exit code for pipelines
```

`$TestDrive` = temp folder auto-cleaned per test file.

## Related
- [[Testing MOC]] · [[Test Doubles]] · [[pytest MOC]]
