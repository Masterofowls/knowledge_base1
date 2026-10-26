---
tags: [curl, http, snippet]
aliases: [curl cookie jar, curl -b -c]
created: 2026-10-26
status: evergreen
---
# curl Cookies and Sessions

```bash
# Log in, save cookies to a jar
curl -c cookies.txt -d "username=ada&password=s3cret" http://localhost:8000/accounts/login/

# Reuse the jar (and keep updating it)
curl -b cookies.txt -c cookies.txt http://localhost:8000/dashboard

# Send a cookie directly
curl -b "sessionid=abc123; theme=dark" http://localhost:8000/
```

Django needs a CSRF token for form POSTs:
```bash
curl -c jar -b jar http://localhost:8000/accounts/login/ -o /dev/null
CSRF=$(awk '/csrftoken/ {print $7}' jar)
curl -c jar -b jar -H "X-CSRFToken: $CSRF" -H "Referer: http://localhost:8000/accounts/login/" \
     -d "username=ada&password=s3cret&csrfmiddlewaretoken=$CSRF" http://localhost:8000/accounts/login/
```

## Related
- [[Authentication Schemes]] · [[Django Authentication]] · [[Supertest Authentication and Agents]] (`agent` = cookie jar)
