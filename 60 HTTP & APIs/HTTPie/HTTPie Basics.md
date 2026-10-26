---
tags: [httpie, http, snippet]
aliases: [http GET, httpie install]
created: 2026-10-26
status: evergreen
---
# HTTPie Basics

```bash
uv tool install httpie        # or: pipx install httpie / brew install httpie / winget install httpie
```

Syntax: `http [flags] [METHOD] URL [REQUEST_ITEM ...]`

```bash
http httpbin.org/get                         # GET (default without data)
http :8000/items                             # localhost shorthand → http://localhost:8000/items
https api.example.com/users                  # https command
http POST :8000/items name=Pen price:=150    # JSON body
http :8000/items name=Pen                    # data present → POST implied
http PUT :8000/items/1 name=Pen price:=200
http PATCH :8000/items/1 price:=175
http DELETE :8000/items/1
http :8000/search q=="hello world" limit==5  # query params (== )
http --follow :8000/old-path                 # follow redirects
http --check-status :8000/missing            # non-zero exit on 4xx/5xx (3 = 3xx, 4 = 4xx, 5 = 5xx)
http --timeout=5 :8000/slow
```

> [!tip] `xh` is a fast Rust re-implementation with the same syntax (`xh :8000/items`).

## Related
- [[HTTPie Request Items]] · [[HTTP Methods]] · [[curl Basics]]
