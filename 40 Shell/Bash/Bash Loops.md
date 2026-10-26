---
tags: [bash, snippet]
aliases: [for loop, while read]
created: 2026-10-26
status: evergreen
---
# Bash Loops

```bash
for i in {1..5}; do echo "$i"; done
for ((i = 0; i < 10; i += 2)); do echo "$i"; done
for f in *.log; do gzip "$f"; done              # glob (use shopt -s nullglob if none)
for host in "${hosts[@]}"; do ping -c1 "$host"; done

# Read a file line by line (correctly)
while IFS= read -r line; do
  echo "→ $line"
done < input.txt

# Read command output without a subshell losing variables
while IFS=, read -r name email; do
  echo "$name <$email>"
done < <(tail -n +2 users.csv)

# Retry loop
for attempt in {1..5}; do
  curl -fsS http://localhost:8000/health && break
  sleep $((attempt * 2))
done

until pg_isready -h db; do sleep 1; done
```

`break`, `continue` work as expected.

> [!warning] `for line in $(cat file)` splits on **words**, not lines. Use `while read`.

## Related
- [[Bash Conditionals and Tests]] · [[Bash find and xargs]] · [[PowerShell Control Flow]]
