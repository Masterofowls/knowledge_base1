---
tags: [moc, testing]
aliases: [Testing, Testing Fundamentals]
created: 2026-10-26
---
# Testing MOC

> [!abstract] Scope
> Tool-agnostic testing concepts, then tool-specific maps.

## Concepts
- [[Testing Pyramid]] — unit / integration / E2E balance
- [[Arrange-Act-Assert]] — test structure
- [[Test Doubles]] — dummy, stub, spy, mock, fake
- [[Test Isolation and Flakiness]]
- [[Test-Driven Development]]
- [[Code Coverage]]
- [[API Testing Strategy]] — what to test on an HTTP API

## Tools
| Language | Unit/Integration | HTTP API | Browser E2E |
|---|---|---|---|
| Python | [[pytest MOC]] | [[FastAPI Testing]], [[Django Testing]] | [[Playwright MOC]] (Python) |
| JS/TS | [[Jest MOC]], [[Redux Testing]] | [[Supertest MOC]] (+ [[Express MOC]]) | [[Playwright MOC]] |
| PowerShell | [[Pester Testing]] | | |
| CLI / manual | | [[curl MOC]], [[HTTPie MOC]] | |
| Contract | | [[OpenAPI Tooling]] (Schemathesis, Prism) | |

## Cross-links
- [[000 Home]]
