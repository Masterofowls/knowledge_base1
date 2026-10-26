---
tags: [curl, http, snippet]
aliases: [curl POST, curl json, curl -d, curl -F]
created: 2026-10-26
status: evergreen
---
# curl Sending Data

```bash
# JSON (curl 7.82+): sets Content-Type + Accept to application/json, method POST
curl --json '{"name":"Pen","price":150}' http://localhost:8000/items

# JSON the long way
curl -X POST http://localhost:8000/items \
  -H "Content-Type: application/json" \
  -d '{"name":"Pen","price":150}'

# Body from file / stdin
curl --json @item.json http://localhost:8000/items
jq -n --arg n "$NAME" '{name:$n}' | curl --json @- http://localhost:8000/items

# Other methods
curl -X PUT    --json '{"name":"Pen","price":200}' http://localhost:8000/items/1
curl -X PATCH  --json '{"price":175}'              http://localhost:8000/items/1
curl -X DELETE http://localhost:8000/items/1

# URL-encoded form (default for -d)
curl -d "username=ada" -d "password=s3cret" http://localhost:8000/auth/token
curl --data-urlencode "comment=hello & goodbye" http://localhost:8000/comments

# Multipart (file upload)
curl -F "title=Avatar" -F "file=@./avatar.png;type=image/png" http://localhost:8000/upload

# Raw binary
curl --data-binary @dump.bin -H "Content-Type: application/octet-stream" http://localhost:8000/blob
```

> [!warning] `-d` implies POST and `application/x-www-form-urlencoded`. `-d @file` strips newlines — use `--data-binary @file` to send exactly.

## Related
- [[HTTP Content Types]] · [[HTTP Methods]] · [[jq Cheat Sheet]]
