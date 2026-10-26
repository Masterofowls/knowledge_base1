---
tags: [curl, guide]
aliases: [Learn curl]
created: 2026-10-26
---
# curl Getting Started

## 1. Check it's installed
```bash
curl --version          # Windows 10+: use curl.exe in PowerShell
```
Upgrade: `winget install cURL.cURL` / `brew install curl`.

## 2. The 10 commands that cover 90% of use
```bash
curl https://httpbin.org/get                                  # GET
curl -i https://httpbin.org/get                               # + response headers
curl -v https://httpbin.org/get                               # full debug
curl -L http://github.com                                     # follow redirects
curl -o out.json https://httpbin.org/json                     # save to file
curl --json '{"a":1}' https://httpbin.org/post                # POST JSON
curl -d "a=1&b=2" https://httpbin.org/post                    # POST form
curl -F "file=@photo.jpg" https://httpbin.org/post            # upload
curl -H "Authorization: Bearer $TOKEN" https://httpbin.org/bearer
curl -fsS -o /dev/null -w "%{http_code}\n" https://httpbin.org/status/418
```

## 3. Learning checklist
- [ ] [[curl Basics]]
- [ ] [[curl Sending Data]]
- [ ] [[curl Headers and Auth]]
- [ ] [[curl Cookies and Sessions]]
- [ ] [[curl Debugging and Timing]]
- [ ] [[curl Scripting Patterns]]
- [ ] [[curl in PowerShell]] (Windows users!)
- [ ] Pair with [[jq Cheat Sheet]]
- [ ] Practise: [[Tutorial - Exploring an API with curl and HTTPie]]

## 4. Beginner mistakes
- Unquoted URLs containing `&` (shell runs part of it in the background).
- `-d` with JSON but no `Content-Type` header (use `--json`).
- Expecting curl to fail on 404 (it doesn't without `-f`).
- Single quotes in Windows `cmd.exe`.

## Resources
- `curl --help all`, `man curl`
- everything.curl.dev — the free book by curl's author
