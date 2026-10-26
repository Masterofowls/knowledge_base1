---
tags: [tutorial, curl, httpie, http]
aliases: [curl tutorial, HTTPie tutorial, API exploration tutorial]
created: 2026-10-26
level: beginner
time: 1h
---
# Tutorial - Exploring an API with curl and HTTPie

> [!abstract] What you'll learn
> How to explore and debug a REST API from the terminal: reading status codes and headers, sending JSON and forms, authentication, cookies, pagination, and timing — each step shown with both **curl** and **HTTPie** so you learn both.

**Practice targets (free, no signup):** `https://httpbin.org` (echoes requests back), `https://jsonplaceholder.typicode.com` (fake REST API), `https://api.github.com` (real API, rate-limited). Or run your API from [[Tutorial - FastAPI CRUD Service with pytest]].

## Step 1 — Your first requests
```bash
curl https://jsonplaceholder.typicode.com/posts/1
http https://jsonplaceholder.typicode.com/posts/1
```
Notice HTTPie colours the output and shows headers by default. Now look at the headers with curl:
```bash
curl -i https://jsonplaceholder.typicode.com/posts/1
```
> [!question] Find in the output: the status code, `Content-Type`, and a caching header. → [[HTTP Headers]]

## Step 2 — Status codes and failures
```bash
curl -s -o /dev/null -w "%{http_code}\n" https://jsonplaceholder.typicode.com/posts/99999
http --check-status https://httpbin.org/status/404; echo "exit: $?"
curl -f https://httpbin.org/status/500; echo "exit: $?"
```
In scripts, a non-zero exit on HTTP errors is what you want ([[HTTP Status Codes]]).

## Step 3 — Query parameters
```bash
curl -G https://jsonplaceholder.typicode.com/comments --data-urlencode "postId=1" | jq length
http https://jsonplaceholder.typicode.com/comments postId==1 | jq length
```

## Step 4 — Sending JSON
```bash
curl --json '{"title":"Hello","body":"From curl","userId":1}' https://jsonplaceholder.typicode.com/posts
http POST https://jsonplaceholder.typicode.com/posts title=Hello body="From HTTPie" userId:=1
```
`userId:=1` sends a number; `userId=1` would send the string `"1"`. Check what was actually sent:
```bash
http -v POST https://httpbin.org/post title=Hello userId:=1 tags:='["a","b"]'
http --offline POST https://httpbin.org/post nested[key]=value     # build without sending
```

## Step 5 — Forms and file uploads
```bash
curl -d "name=Ada&lang=en" https://httpbin.org/post | jq .form
http -f POST https://httpbin.org/post name=Ada lang=en | jq .form

echo "hello" > note.txt
curl -F "file=@note.txt" -F "title=Note" https://httpbin.org/post | jq .files
http -f POST https://httpbin.org/post file@note.txt title=Note | jq .files
```
→ [[HTTP Content Types]]

## Step 6 — Authentication
```bash
curl -u user:passwd https://httpbin.org/basic-auth/user/passwd
http -a user:passwd https://httpbin.org/basic-auth/user/passwd

curl -H "Authorization: Bearer my-token" https://httpbin.org/bearer
http -A bearer -a my-token https://httpbin.org/bearer
```
→ [[Authentication Schemes]]

## Step 7 — Cookies and sessions
```bash
curl -c jar.txt https://httpbin.org/cookies/set/session/abc123 -L
curl -b jar.txt https://httpbin.org/cookies

http --session=demo https://httpbin.org/cookies/set/session/abc123 --follow
http --session=demo https://httpbin.org/cookies
```

## Step 8 — Pagination and rate limits on a real API
```bash
curl -sI "https://api.github.com/users/torvalds/repos?per_page=5" | grep -iE '^(link|x-ratelimit)'
http -h "https://api.github.com/users/torvalds/repos" per_page==5 | grep -iE 'link|ratelimit'
```
The `Link` header tells you the URL of the next page; `X-RateLimit-Remaining` how many calls you have left.

## Step 9 — Debugging and timing
```bash
curl -v https://httpbin.org/get 2>&1 | grep -E '^[<>*]' | head -30
curl -s -o /dev/null -w "dns %{time_namelookup}s connect %{time_connect}s tls %{time_appconnect}s ttfb %{time_starttransfer}s total %{time_total}s\n" https://httpbin.org/delay/1
http --meta https://httpbin.org/delay/1
```
→ [[curl Debugging and Timing]]

## Step 10 — Put it together: a smoke-test script
```bash
#!/usr/bin/env bash
set -euo pipefail
BASE="${1:-http://localhost:8000}"
id=$(curl -fsS --json '{"url":"https://example.com","title":"Smoke"}' "$BASE/bookmarks" | jq -r .id)
curl -fsS "$BASE/bookmarks/$id" | jq -e '.title == "Smoke"' > /dev/null
curl -fsS -X DELETE "$BASE/bookmarks/$id" -o /dev/null
code=$(curl -s -o /dev/null -w "%{http_code}" "$BASE/bookmarks/$id")
[[ "$code" == 404 ]] && echo "✅ smoke test passed" || { echo "❌ expected 404, got $code"; exit 1; }
```

## 🏋️ Challenges
1. On Windows, repeat steps 1–6 with `Invoke-RestMethod` ([[PowerShell Web Requests]], [[curl in PowerShell]]).
2. Use browser DevTools "Copy as cURL" on any site, then convert the command to HTTPie.
3. Write a script that follows GitHub `Link` headers to download all pages of a user's repos.
4. Use `curl --resolve` to hit a specific IP for a hostname.

## Related
- [[Tutorials MOC]] · [[HTTPie vs curl]] · [[jq Cheat Sheet]]
