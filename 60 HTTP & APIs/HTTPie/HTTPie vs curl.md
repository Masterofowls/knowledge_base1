---
tags: [httpie, curl, comparison]
aliases: [curl vs httpie]
created: 2026-10-26
status: evergreen
---
# HTTPie vs curl

| Task | curl | HTTPie |
|---|---|---|
| GET | `curl -s localhost:8000/items` | `http :8000/items` |
| JSON POST | `curl --json '{"name":"Pen","price":150}' localhost:8000/items` | `http :8000/items name=Pen price:=150` |
| query params | `curl -G url --data-urlencode "q=a b"` | `http url q=="a b"` |
| header | `-H "X-Key: 1"` | `X-Key:1` |
| bearer | `-H "Authorization: Bearer $T"` | `-A bearer -a $T` |
| form | `-d a=1` | `-f a=1` |
| upload | `-F file=@x.png` | `-f file@x.png` |
| show headers | `-i` / `-v` | `-h` / `-v` |
| follow redirects | `-L` | `--follow` |
| fail on 4xx/5xx | `-f` | `--check-status` |
| cookies across calls | `-b jar -c jar` | `--session=name` |
| timing | `-w "%{time_total}"` | `--meta` |

**Use curl when:** scripting/CI, it's preinstalled everywhere, advanced transport options (TLS, proxies, `--resolve`, HTTP/3), "Copy as cURL" from DevTools.
**Use HTTPie when:** exploring APIs interactively, readable docs/examples, nested JSON from the command line.

## Related
- [[curl MOC]] · [[HTTPie MOC]] · [[PowerShell Web Requests]]
