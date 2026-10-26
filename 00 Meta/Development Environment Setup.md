---
tags: [meta, guide]
aliases: [Dev setup, Environment setup, Install everything]
created: 2026-10-26
---
# Development Environment Setup

One-time setup for every tutorial in this vault. Windows commands use **winget** (built into Windows 11); macOS uses **Homebrew**; Linux uses your package manager.

## 1. Terminal & shell
| | Windows | macOS | Linux |
|---|---|---|---|
| Terminal | Windows Terminal (built in) | iTerm2 / built-in Terminal | built in |
| PowerShell 7 | `winget install Microsoft.PowerShell` | `brew install powershell/tap/powershell` | see MS docs |
| Bash | `wsl --install` (Ubuntu) or Git Bash | `brew install bash` | built in |

## 2. Core tools
```powershell
# Windows (run in PowerShell)
winget install Git.Git
winget install Microsoft.VisualStudioCode
winget install CoreyButler.NVMforWindows      # then: nvm install lts; nvm use lts
winget install astral-sh.uv                   # Python + packaging
winget install Docker.DockerDesktop
winget install jqlang.jq
winget install HTTPie.HTTPie
winget install BurntSushi.ripgrep.MSVC
winget install koalaman.shellcheck
```
```bash
# macOS
brew install git node uv jq httpie ripgrep shellcheck
brew install --cask visual-studio-code docker
```
```bash
# Ubuntu / Debian (WSL included)
sudo apt update && sudo apt install -y git jq ripgrep shellcheck curl build-essential
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash && nvm install --lts
curl -LsSf https://astral.sh/uv/install.sh | sh
uv tool install httpie
```

## 3. Verify
```bash
git --version && node -v && npm -v && uv --version && docker --version && jq --version && http --version
uv python install 3.13 && uv run --python 3.13 python -V
```

## 4. Git first-time config
```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
git config --global core.autocrlf input     # Windows: avoid CRLF in scripts (use "true" if you prefer CRLF checkouts)
```

## 5. VS Code extensions
ESLint · Prettier · Python · Ruff · Pylance · Playwright Test · PowerShell · ShellCheck · REST Client or Thunder Client · YAML (Red Hat) · OpenAPI (42Crunch or Redocly) · SQLTools · Docker · GitLens.

## 6. Databases
```bash
docker run -d --name pg -e POSTGRES_PASSWORD=pw -p 5432:5432 -v pgdata:/var/lib/postgresql/data postgres:17
docker exec -it pg psql -U postgres
```
GUI: DBeaver (`winget install dbeaver.dbeaver`).

## 7. Browser
Chrome/Edge/Firefox with **React DevTools** and **Redux DevTools** extensions. Learn the DevTools Network tab ([[HTTP Getting Started]]).

## 8. Project folder convention
```
~/code/
├── learning/          # tutorial projects from this vault
│   ├── bookmarks-api/
│   ├── task-board/
│   └── ...
└── work/
```
Link a tutorial project from its tutorial note: add a line `Local code: file:///C:/Users/me/code/learning/bookmarks-api`.

> [!tip] Windows line endings: if a Bash script fails with `$'\r': command not found`, convert it to LF (`dos2unix script.sh` or VS Code status bar → CRLF → LF).

## Related
- [[How to Study with This Vault]] · [[Tutorials MOC]] · [[Package Managers Compared]]
