---
tags: [httpie, guide]
aliases: [Learn HTTPie]
created: 2026-10-26
---
# HTTPie Getting Started

## 1. Install
```bash
uv tool install httpie       # or pipx install httpie / brew install httpie / winget install HTTPie.HTTPie
http --version
```
There's also **HTTPie Desktop** (GUI) and the faster Rust clone **xh** (`winget install ducaale.xh`).

## 2. First requests
```bash
http httpbin.org/get
http httpbin.org/get search==hello
http POST httpbin.org/post name=Ada age:=36 admin:=true
http -f POST httpbin.org/post name=Ada
http -a user:pass httpbin.org/basic-auth/user/pass
http -v :8000/docs                       # localhost shorthand
```

## 3. Learning checklist
- [ ] [[HTTPie Basics]]
- [ ] [[HTTPie Request Items]] — memorise `:` `==` `=` `:=` `@`
- [ ] [[HTTPie Authentication and Sessions]]
- [ ] [[HTTPie Output Options]]
- [ ] [[HTTPie vs curl]]
- [ ] Practise: [[Tutorial - Exploring an API with curl and HTTPie]]

## 4. Beginner mistakes
- `age=36` sends a string — use `age:=36` for numbers.
- Forgetting HTTPie switches to POST when you add data items.
- Shell quoting nested JSON / brackets, especially in PowerShell.

## Resources
- httpie.io/docs/cli
