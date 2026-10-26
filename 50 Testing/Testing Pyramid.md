---
tags: [testing, concept]
aliases: [test pyramid, testing trophy]
created: 2026-10-26
status: evergreen
---
# Testing Pyramid

> [!summary] TL;DR
> Many fast, cheap **unit** tests; fewer **integration** tests; a handful of slow, expensive **end-to-end** tests. Push each check to the lowest level that can catch the bug.

```
            ▲  E2E (browser, full stack)      — few, slow, high confidence per test
           ▲▲▲  Integration (API + DB, components)
        ▲▲▲▲▲▲▲  Unit (functions, classes)      — many, fast, precise failures
```

| Level | Scope | Speed | Tools |
|---|---|---|---|
| Unit | one function/class, deps doubled | ms | [[pytest MOC\|pytest]], [[Jest MOC\|Jest]] |
| Integration | several units + real DB/HTTP layer | 10–500 ms | [[FastAPI Testing]], [[Supertest MOC\|Supertest]], [[Django Testing]] |
| Contract | API matches spec | fast | Schemathesis, Pact, [[OpenAPI Tooling]] |
| E2E | user journeys through real UI | seconds | [[Playwright MOC\|Playwright]] |

## Testing Trophy (Kent C. Dodds)
For frontend apps: static analysis (TS, ESLint) at the base, then unit, **most weight on integration**, small E2E top. "Write tests. Not too many. Mostly integration."

> [!warning] The ice-cream cone anti-pattern: mostly manual + E2E tests, few unit tests → slow, flaky, expensive feedback.

## Related
- [[Test Doubles]] · [[Test Isolation and Flakiness]] · [[API Testing Strategy]]
