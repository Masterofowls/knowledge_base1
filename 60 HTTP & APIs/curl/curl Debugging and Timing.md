---
tags: [curl, http, snippet]
aliases: [curl -v, curl -w, curl trace, curl timing]
created: 2026-10-26
status: evergreen
---
# curl Debugging and Timing

```bash
curl -v https://api.example.com/users        # > request headers, < response headers, * TLS/connection info
curl --trace-ascii - https://api.example.com  # everything including bodies
curl -s -o /dev/null -w "%{http_code}\n" https://api.example.com/health   # just the status code

# Timing breakdown
curl -s -o /dev/null -w @- https://api.example.com <<'EOF'
     dns:  %{time_namelookup}s
 connect:  %{time_connect}s
     tls:  %{time_appconnect}s
    ttfb:  %{time_starttransfer}s
   total:  %{time_total}s
    size:  %{size_download} bytes
  status:  %{http_code}
EOF

# Force resolution / bypass DNS (test a specific backend)
curl --resolve api.example.com:443:10.0.0.12 https://api.example.com/health
curl --connect-to api.example.com:443:staging-lb:443 https://api.example.com/

# HTTP version
curl --http2 -I https://example.com
curl --http3 -I https://cloudflare.com        # if built with HTTP/3

# Through a proxy (e.g. mitmproxy / Burp / Fiddler on :8080)
curl -x http://127.0.0.1:8080 -k https://api.example.com
```

> [!tip] Browser DevTools → Network → right-click a request → **Copy as cURL** gives you a reproducible command.

## Related
- [[HTTP Request and Response Anatomy]] · [[curl Scripting Patterns]] · [[Playwright Debugging and Trace Viewer]]
