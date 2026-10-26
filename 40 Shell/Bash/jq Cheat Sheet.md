---
tags: [bash, snippet, http]
aliases: [jq]
created: 2026-10-26
status: evergreen
---
# jq Cheat Sheet

```bash
curl -s https://api.github.com/repos/python/cpython | jq '.stargazers_count'

jq '.'                              # pretty print
jq -r '.name'                       # raw string (no quotes)
jq '.items[0]'                      # first element
jq '.items[] | .name'               # iterate
jq '[.items[] | select(.price > 10) | {name, price}]'
jq '.items | length'
jq '.items | map(.price) | add'
jq '.items | sort_by(.price) | reverse | .[:3]'
jq 'group_by(.category) | map({category: .[0].category, count: length})'
jq -r '.users[] | [.id, .email] | @csv'
jq --arg id "$ID" '.items[] | select(.id == $id)'
jq '.config.debug = true' in.json > out.json
jq -n --arg n "Ada" '{name: $n, active: true}'     # build JSON safely for curl -d
```

> [!tip] Building request bodies with `jq -n` avoids quoting bugs: `curl -d "$(jq -n --arg e "$EMAIL" '{email:$e}')"`.

## Related
- [[curl MOC]] · [[HTTPie MOC]] · [[PowerShell Pipeline and Objects]] (`ConvertFrom-Json`)
