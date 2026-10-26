---
tags: [bash, snippet]
aliases: [grep, sed, awk, text processing, sort uniq cut]
created: 2026-10-26
status: evergreen
---
# Bash grep sed awk

## grep — find lines
```bash
grep -rn "TODO" src/                  # recursive, line numbers
grep -i "error" app.log               # case-insensitive
grep -v "DEBUG" app.log               # invert
grep -E "5[0-9]{2} " access.log       # extended regex
grep -c ERROR app.log                 # count
grep -o '"id":[0-9]*' data.json       # only matching part
grep -A3 -B1 Exception app.log        # context
rg "pattern" -t py                    # ripgrep: faster, respects .gitignore
```

## sed — stream edit
```bash
sed 's/foo/bar/g' in.txt              # replace all on each line
sed -i 's/localhost/db/g' .env        # in place (macOS: sed -i '' ...)
sed -n '10,20p' file                  # print lines 10–20
sed '/^#/d' config                    # delete comment lines
sed -E 's/([0-9]+)px/\1rem/g' style.css
```

## awk — columns & reports
```bash
awk '{print $1, $9}' access.log                       # fields (space-separated)
awk -F, 'NR > 1 {sum += $3} END {print sum}' sales.csv   # sum column 3, skip header
awk '$9 >= 500 {count[$7]++} END {for (u in count) print count[u], u}' access.log | sort -rn | head
```

## The rest of the toolbox
```bash
cut -d, -f1,3 data.csv
sort -t, -k3 -n data.csv | uniq -c | sort -rn | head    # frequency count
tr 'a-z' 'A-Z'; tr -d '\r'                               # fix Windows line endings
wc -l; head; tail; column -t; paste; comm; diff -u
```

> [!example] Top 10 IPs hitting your server
> ```bash
> awk '{print $1}' access.log | sort | uniq -c | sort -rn | head -10
> ```

## Related
- [[Bash Redirection and Pipes]] · [[jq Cheat Sheet]] · [[PowerShell Pipeline and Objects]] (`Select-String`)
