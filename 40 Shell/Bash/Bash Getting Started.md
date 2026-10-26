---
tags: [bash, guide]
aliases: [Learn Bash, Bash roadmap]
created: 2026-10-26
---
# Bash Getting Started

## 1. Get a Bash shell
| OS | How |
|---|---|
| Linux | already there (`bash --version`) |
| macOS | default is zsh; `brew install bash` for Bash 5 |
| Windows | **WSL**: `wsl --install` (full Linux), or **Git Bash** (ships with Git for Windows) |

Install helpers: `shellcheck`, `jq`, `ripgrep`, `fd`, `tree`.

## 2. Hello script
```bash
#!/usr/bin/env bash
set -euo pipefail
name="${1:-world}"
echo "Hello, $name! Today is $(date +%A)."
```
```bash
chmod +x hello.sh && ./hello.sh Ada
```

## 3. Learning checklist
- [ ] Navigation & files → [[Bash Navigation and Files]]
- [ ] Variables & quoting → [[Bash Variables and Quoting]]
- [ ] Redirection & pipes → [[Bash Redirection and Pipes]]
- [ ] Conditionals → [[Bash Conditionals and Tests]]
- [ ] Loops → [[Bash Loops]]
- [ ] Functions → [[Bash Functions]]
- [ ] grep / sed / awk → [[Bash grep sed awk]]
- [ ] find & xargs → [[Bash find and xargs]]
- [ ] Processes & jobs → [[Bash Processes and Jobs]]
- [ ] JSON with jq → [[jq Cheat Sheet]]
- [ ] Robust scripts → [[Bash Script Template]]
- [ ] Practise: [[Tutorial - Bash Backup Script]]

## 4. Beginner mistakes
- Unquoted variables (`rm $file`).
- Spaces around `=` (`x = 1`).
- Parsing `ls` output; `for line in $(cat f)`.
- Ignoring exit codes; no `set -euo pipefail`.
- Windows line endings (`\r`) in scripts → `bad interpreter` errors. Fix: `dos2unix` or editor setting LF.

## Resources
- `man bash`, `help <builtin>`, tldr.sh (`tldr tar`)
- Bash Guide — mywiki.wooledge.org/BashGuide (and BashPitfalls)
- explainshell.com — paste a command to see what each part does
- shellcheck.net
