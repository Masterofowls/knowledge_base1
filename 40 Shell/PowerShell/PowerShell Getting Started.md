---
tags: [powershell, guide]
aliases: [Learn PowerShell, PowerShell roadmap]
created: 2026-10-26
---
# PowerShell Getting Started

## 1. Install PowerShell 7
```powershell
winget install Microsoft.PowerShell       # Windows (5.1 is built in; 7 is the modern one: `pwsh`)
```
```bash
brew install powershell/tap/powershell    # macOS
```
Use **Windows Terminal** and the VS Code **PowerShell** extension. Allow local scripts once:
```powershell
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
```

## 2. Hello script
```powershell
# hello.ps1
param([string]$Name = 'world')
"Hello, $Name! Today is $((Get-Date).DayOfWeek)."
Get-Process | Sort-Object CPU -Descending | Select-Object -First 3 Name, CPU
```
```powershell
./hello.ps1 -Name Ada
```

## 3. Learning checklist
- [ ] Cmdlets & discovery (`Get-Help`, `Get-Command`, `Get-Member`) → [[PowerShell Basics and Cmdlets]]
- [ ] Objects in the pipeline → [[PowerShell Pipeline and Objects]]
- [ ] Variables, arrays, hashtables, operators → [[PowerShell Variables and Types]]
- [ ] Control flow → [[PowerShell Control Flow]]
- [ ] Advanced functions → [[PowerShell Functions and Parameters]]
- [ ] Error handling → [[PowerShell Error Handling]]
- [ ] File system → [[PowerShell File System]]
- [ ] REST APIs → [[PowerShell Web Requests]]
- [ ] Modules & profile → [[PowerShell Modules and Profile]]
- [ ] Testing with Pester → [[Pester Testing]]
- [ ] Coming from Bash → [[Bash vs PowerShell Rosetta]]
- [ ] Practise: [[Tutorial - PowerShell Log Cleanup with Pester]]

## 4. Beginner mistakes
- Using `==`, `>`, `<` instead of `-eq`, `-gt`, `-lt` (`>` redirects to a file!).
- Piping `Format-Table` output into other commands.
- Expecting `try/catch` to catch non-terminating errors (add `-ErrorAction Stop`).
- `curl` meaning `Invoke-WebRequest` in Windows PowerShell 5.1.
- Accidental output from functions (`$list.Add(1)` returns an index → `[void]`).

## Resources
- learn.microsoft.com/powershell — docs & "PowerShell 101"
- "Learn PowerShell in a Month of Lunches"
- Pester docs — pester.dev
