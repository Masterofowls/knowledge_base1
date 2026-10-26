---
tags: [curl, http, snippet]
aliases: [curl -H, curl bearer, curl basic auth]
created: 2026-10-26
status: evergreen
---
# curl Headers and Auth

```bash
curl -H "Accept: application/json" -H "X-Request-ID: $(uuidgen)" https://api.example.com/me

# Bearer token
curl -H "Authorization: Bearer $TOKEN" https://api.example.com/me
curl --oauth2-bearer "$TOKEN" https://api.example.com/me

# Basic auth
curl -u ada:s3cret https://api.example.com/admin
curl -u ada https://api.example.com/admin          # prompts for password (keeps it out of history)

# API key
curl -H "X-API-Key: $API_KEY" https://api.example.com/data

# Get a token, then use it
TOKEN=$(curl -fsS -d "username=ada&password=$PW" http://localhost:8000/auth/token | jq -r .access_token)
curl -H "Authorization: Bearer $TOKEN" http://localhost:8000/me

# Headers from a file (keeps secrets out of `ps` output)
curl -H @headers.txt https://api.example.com/me

# Remove a default header / set user agent
curl -H "User-Agent:" https://example.com
curl -A "my-script/1.0" https://example.com
```

> [!warning] Tokens on the command line end up in shell history and process lists. Prefer env vars, `-H @file`, or `--netrc`.

## Related
- [[Authentication Schemes]] · [[HTTP Headers]] · [[HTTPie Authentication and Sessions]]
