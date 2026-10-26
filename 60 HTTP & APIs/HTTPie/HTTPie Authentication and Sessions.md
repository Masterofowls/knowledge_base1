---
tags: [httpie, http, snippet]
aliases: [httpie auth, httpie session]
created: 2026-10-26
status: evergreen
---
# HTTPie Authentication and Sessions

```bash
http -a ada:s3cret :8000/admin                     # Basic
http -a ada :8000/admin                            # prompt for password
http -A bearer -a "$TOKEN" :8000/me                # Bearer
http :8000/me "Authorization:Bearer $TOKEN"        # same, as a header
http :8000/data X-API-Key:$API_KEY
```

## Sessions — persist headers, auth & cookies between calls
```bash
http --session=dev -a ada:s3cret :8000/me          # creates ~/.config/httpie/sessions/localhost_8000/dev.json
http --session=dev :8000/orders                    # reuses auth + cookies
http --session=./api-session.json :8000/me         # session file path
http --session-read-only=dev :8000/me              # don't update it
```

Grab a token then reuse:
```bash
TOKEN=$(http -f POST :8000/auth/token username=ada password=s3cret | jq -r .access_token)
http :8000/me -A bearer -a "$TOKEN"
```

## Related
- [[Authentication Schemes]] · [[curl Headers and Auth]] · [[curl Cookies and Sessions]]
