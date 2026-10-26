---
tags: [httpie, http, snippet]
aliases: [httpie --print, httpie offline, httpie download]
created: 2026-10-26
status: evergreen
---
# HTTPie Output Options

```bash
http -v :8000/items                 # full request + response
http -h :8000/items                 # response headers only
http -b :8000/items                 # response body only (pipe-friendly)
http --print=HhBb :8000/items       # H=req headers, B=req body, h=resp headers, b=resp body, m=meta
http --meta :8000/items             # timing info
http --offline POST :8000/items name=Pen   # build & print request, don't send (great for docs)
http --pretty=none :8000/items      # no colours/formatting
http --sorted :8000/items           # sort headers and JSON keys
http -d https://example.com/file.zip   # download with progress bar (-o name)
http --stream :8000/events          # stream SSE / long responses
http :8000/items | jq '.[0]'        # output is plain JSON when piped
```

## Related
- [[HTTPie Basics]] · [[jq Cheat Sheet]] · [[curl Debugging and Timing]]
