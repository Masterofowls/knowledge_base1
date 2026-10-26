---
tags: [bash, concept]
aliases: [quoting, parameter expansion, command substitution]
created: 2026-10-26
status: evergreen
---
# Bash Variables and Quoting

> [!summary] TL;DR
> No spaces around `=`. **Always double-quote expansions** (`"$var"`) unless you specifically want word splitting and globbing.

```bash
name="Ada"                 # no spaces!
readonly VERSION=1.2
export API_URL="https://api.example.com"    # visible to child processes
today=$(date +%F)          # command substitution
count=$(( 3 + 4 * 2 ))     # arithmetic

echo "Hello $name"         # expands
echo 'Hello $name'         # literal
echo "Price: \$5"
echo "${name}_backup"      # braces delimit the name
```

## Quoting matters
```bash
file="my report.txt"
rm $file       # ❌ tries to remove "my" and "report.txt"
rm "$file"     # ✅
for f in "$@"; do ...      # ✅ preserves each argument
```

## Parameter expansion
| Syntax | Result |
|---|---|
| `${var:-default}` | default if unset/empty |
| `${var:=default}` | assign default |
| `${var:?msg}` | error if unset |
| `${#var}` | length |
| `${var#prefix}` / `${var##*/}` | strip shortest/longest prefix (basename) |
| `${var%suffix}` / `${var%.*}` | strip suffix (remove extension) |
| `${var/old/new}` / `${var//old/new}` | replace first / all |
| `${var^^}` / `${var,,}` | upper / lower |

## Special variables
`$0` script name · `$1..$9` args · `$#` count · `"$@"` all args (separately) · `$?` last exit code · `$$` PID · `$!` last background PID

## Arrays
```bash
fruits=(apple "dragon fruit" cherry)
echo "${fruits[1]}"; echo "${#fruits[@]}"
for f in "${fruits[@]}"; do echo "$f"; done
declare -A port=([web]=80 [db]=5432); echo "${port[db]}"
```

## Related
- [[Bash Conditionals and Tests]] · [[PowerShell Variables and Types]]
