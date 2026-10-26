---
tags: [testing, concept]
aliases: [mocks, stubs, spies, fakes, mocking]
created: 2026-10-26
status: evergreen
---
# Test Doubles

> [!summary] TL;DR
> A test double replaces a real dependency in a test. "Mock" is used loosely for all of them, but they differ in *what they verify*.

| Double | What it does | Example |
|---|---|---|
| **Dummy** | passed but never used | `None` for an unused logger |
| **Stub** | returns canned answers | `get_rate() → 1.1` |
| **Spy** | records calls (often wraps real impl) | `jest.spyOn(console, "error")` |
| **Mock** | pre-programmed expectations, verifies interactions | `mock.assert_called_once_with(...)` |
| **Fake** | working lightweight implementation | in-memory repo, SQLite instead of Postgres |

## Per tool
| | Python | Jest | Playwright | PowerShell |
|---|---|---|---|---|
| stub/mock fn | `unittest.mock.Mock`, `monkeypatch` | `jest.fn()` | — | `Mock` |
| patch module | `mocker.patch("app.x.y")` | `jest.mock("./x")` | — | `Mock -ModuleName` |
| network | `respx`, `responses` | `msw`, `nock` | `page.route()` | |
| time | `freezegun`, `time-machine` | `jest.useFakeTimers()` | `page.clock` | |

See [[pytest Mocking]], [[Jest Mock Functions]], [[Jest Module Mocking]], [[Playwright Network Mocking]].

> [!warning] Over-mocking
> - Mock at **boundaries** you don't own (HTTP, clock, randomness, email) — not your own internal classes.
> - Asserting on call details couples tests to implementation; prefer asserting on outcomes.
> - "Don't mock what you don't own" → wrap third-party APIs in an adapter and fake the adapter.

## Related
- [[Testing Pyramid]] · [[Test Isolation and Flakiness]]
