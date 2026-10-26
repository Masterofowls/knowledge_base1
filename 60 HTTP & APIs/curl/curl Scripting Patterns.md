---
tags: [curl, bash, snippet]
aliases: [curl retry, curl health check, .curlrc]
created: 2026-10-26
status: evergreen
---
# curl Scripting Patterns

```bash
# Wait until a service is healthy (CI / docker-compose)
curl --retry 30 --retry-delay 2 --retry-all-errors -fsS http://localhost:8000/health

# Capture status and body separately
response=$(curl -sS -w "\n%{http_code}" http://localhost:8000/items/1)
body=$(sed '$d' <<< "$response")
status=$(tail -n1 <<< "$response")
[[ "$status" == 200 ]] || { echo "failed ($status): $body" >&2; exit 1; }

# Smoke test a list of endpoints
for path in / /health /docs /openapi.json; do
  code=$(curl -s -o /dev/null -w "%{http_code}" "http://localhost:8000$path")
  printf '%-15s %s\n' "$path" "$code"
done

# Parallel requests (curl 7.66+)
curl --parallel --parallel-max 10 -o "out_#1.json" "https://api.example.com/items/[1-50]"

# Config file to avoid repetition
cat > api.curlrc <<'EOF'
header = "Accept: application/json"
header = "Authorization: Bearer REPLACE_ME"
silent
show-error
fail-with-body
EOF
curl -K api.curlrc http://localhost:8000/me
```

## Related
- [[Bash Script Template]] · [[Bash Loops]] · [[jq Cheat Sheet]] · [[curl Debugging and Timing]]
