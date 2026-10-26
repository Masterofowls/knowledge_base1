---
tags: [testing, concept]
aliases: [flaky tests, test isolation]
created: 2026-10-26
status: evergreen
---
# Test Isolation and Flakiness

> [!summary] TL;DR
> A test must pass or fail the same way regardless of order, timing, machine, or what ran before. Flaky tests erode trust faster than missing tests.

## Common causes → fixes
| Cause | Fix |
|---|---|
| Shared mutable state (globals, DB rows, files) | fresh fixtures per test; transaction rollback; `tmp_path` |
| Order dependence | run in random order (`pytest-randomly`, `jest --randomize`) |
| Real time / timezones | inject clock; fake timers; set `TZ=UTC` |
| Randomness | seed it; or assert on properties |
| Network / third-party | stub at the boundary ([[Test Doubles]]) |
| Async races / fixed sleeps | wait for a condition, not a duration ([[Playwright Assertions]] auto-retry) |
| Parallel workers colliding | unique data per worker (IDs, schemas, ports) |
| Resource leaks | close servers/DB pools in teardown |

## Policy
1. Quarantine (tag & skip) flaky tests immediately; file a ticket.
2. Reproduce with repetition: `pytest --count=50` (pytest-repeat), `npx playwright test --repeat-each=20`, `jest --runInBand`.
3. Fix root cause — retries hide bugs (Playwright `retries` in CI is a band-aid, track "flaky" results).

## Related
- [[pytest Fixtures]] · [[Jest Setup and Teardown]] · [[Playwright Debugging and Trace Viewer]]
