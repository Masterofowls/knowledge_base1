---
tags: [bash, snippet]
aliases: [ps, kill, background jobs, nohup, signals]
created: 2026-10-26
status: evergreen
---
# Bash Processes and Jobs

```bash
long_task &               # run in background
jobs                      # list jobs
fg %1; bg %1              # foreground / background
Ctrl+Z                    # suspend current job
wait                      # wait for all background jobs
nohup ./server > server.log 2>&1 &    # survive logout (or use tmux / systemd)

ps aux | grep node
pgrep -fl uvicorn
top / htop
kill 1234                 # SIGTERM (polite)
kill -9 1234              # SIGKILL (last resort)
pkill -f "python app.py"
lsof -i :8000             # who's using port 8000
time ./build.sh
timeout 10s ./flaky.sh
```

## Parallel work
```bash
for f in *.csv; do process "$f" & done; wait
ls *.png | xargs -P 4 -I{} convert {} {}.webp
```

## Signals
| Signal | Number | Meaning |
|---|---|---|
| SIGINT | 2 | Ctrl+C |
| SIGTERM | 15 | please terminate (default `kill`) |
| SIGKILL | 9 | force kill (can't be trapped) |
| SIGHUP | 1 | terminal closed / reload config |

## Related
- [[Bash find and xargs]] · [[PowerShell Basics and Cmdlets]] (`Get-Process`, `Stop-Process`)
