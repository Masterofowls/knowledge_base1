---
tags: [httpie, http, snippet]
aliases: [httpie syntax, httpie request items syntax]
created: 2026-10-26
status: evergreen
---
# HTTPie Request Items

| Separator | Meaning | Example | Result |
|---|---|---|---|
| `:` | header | `X-API-Key:abc` | `X-API-Key: abc` |
| `==` | query param | `page==2` | `?page=2` |
| `=` | JSON string field (or form field with `-f`) | `name=Pen` | `{"name": "Pen"}` |
| `:=` | raw JSON value | `price:=150 tags:='["a","b"]' active:=true` | numbers/bools/arrays/objects |
| `@` | file upload (multipart with `-f`) | `file@./avatar.png` | |
| `=@` | string field from file | `bio=@bio.txt` | |
| `:=@` | JSON field from file | `config:=@config.json` | |

## Nested JSON (HTTPie 3+)
```bash
http POST :8000/orders \
  customer[name]=Ada \
  customer[email]=ada@example.com \
  items[0][sku]=PEN-1 items[0][qty]:=2 \
  'tags[]=gift' 'tags[]=priority'
# → {"customer":{"name":"Ada","email":"ada@example.com"},"items":[{"sku":"PEN-1","qty":2}],"tags":["gift","priority"]}
```

## Forms & raw bodies
```bash
http -f POST :8000/auth/token username=ada password=s3cret      # urlencoded
http -f POST :8000/upload title=Avatar file@avatar.png           # multipart
http POST :8000/items < item.json                                # raw body from stdin
http --raw '{"name":"Pen"}' POST :8000/items
```

> [!warning] Shell quoting: quote items containing spaces, `[]`, `&`, or `;` — especially in PowerShell (`'tags:=["a"]'`).

## Related
- [[HTTP Content Types]] · [[curl Sending Data]]
