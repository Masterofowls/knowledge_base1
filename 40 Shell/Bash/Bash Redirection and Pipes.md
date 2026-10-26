---
tags: [bash, concept]
aliases: [redirection, pipes, stdout stderr, here-doc]
created: 2026-10-26
status: evergreen
---
# Bash Redirection and Pipes

| | |
|---|---|
| `cmd > f` | stdout to file (overwrite) |
| `cmd >> f` | append |
| `cmd 2> err.log` | stderr to file |
| `cmd > out.log 2>&1` / `cmd &> out.log` | both to file |
| `cmd 2>/dev/null` | discard errors |
| `cmd < in.txt` | stdin from file |
| `a \| b` | stdout of a → stdin of b |
| `a \|& b` | stdout+stderr → b |
| `cmd \| tee out.log` | show *and* save |

> [!warning] Order matters: `cmd 2>&1 > f` sends stderr to the terminal; `cmd > f 2>&1` sends both to f.

## Here-docs & here-strings
```bash
cat > config.yml <<EOF
host: $HOST              # variables expanded
EOF

cat <<'EOF'              # quoted delimiter → no expansion
$NOT_EXPANDED
EOF

grep foo <<< "$text"     # here-string
```

## Process substitution
```bash
diff <(sort a.txt) <(sort b.txt)
```

## Pipelines & exit codes
```bash
set -o pipefail                    # pipeline fails if any part fails
cat app.log | grep ERROR | wc -l
echo "${PIPESTATUS[@]}"            # exit code of each stage
```

## Related
- [[Bash grep sed awk]] · [[PowerShell Pipeline and Objects]] — objects instead of text
