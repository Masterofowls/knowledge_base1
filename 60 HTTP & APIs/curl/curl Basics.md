---
tags: [curl, http, snippet]
aliases: [curl GET]
created: 2026-10-26
status: evergreen
---
# curl Basics

```bash
curl https://api.example.com/users                # GET, body to stdout
curl -i https://api.example.com/users             # include response headers
curl -I https://example.com                       # HEAD (headers only)
curl -L http://example.com                        # follow redirects
curl -o page.html https://example.com             # save to file
curl -O https://example.com/file.zip              # save with remote name
curl -sS https://api.example.com/users            # silent but still show errors
curl -f https://api.example.com/missing           # exit code 22 on HTTP >= 400
curl --fail-with-body https://api.example.com/x   # fail but still print error body
curl "https://api.example.com/search?q=hello%20world&limit=5"   # quote URLs with & !
curl -G https://api.example.com/search --data-urlencode "q=hello world" -d limit=5  # build query
curl --max-time 10 --connect-timeout 3 https://slow.example.com
curl -k https://self-signed.local                 # skip TLS verify (dev only!)
```

> [!tip] The scripting trio: `curl -fsSL` = fail on HTTP errors, silent, show errors, follow redirects.

## Related
- [[curl Sending Data]] · [[HTTP Request and Response Anatomy]]
