---
tags: [playwright, testing, snippet]
aliases: [trace viewer, PWDEBUG, UI mode]
created: 2026-10-26
status: evergreen
---
# Playwright Debugging and Trace Viewer

```bash
npx playwright test --ui                      # best local loop: watch, time-travel, pick locator
npx playwright test --debug                   # Inspector, step through
npx playwright test login.spec.ts:42 --headed # one test, visible browser
PWDEBUG=1 npx playwright test                 # same as --debug
npx playwright test --trace on                # force traces
npx playwright show-trace test-results/.../trace.zip   # or trace.playwright.dev
npx playwright test --repeat-each=20 --workers=1       # hunt flakes
npx playwright test --last-failed
```

In code:
```ts
await page.pause();                            // opens Inspector here
test.slow();                                   // triple the timeout
test.info().annotations.push({ type: "issue", description: "JIRA-123" });
page.on("console", (m) => console.log("browser:", m.text()));
```

**Trace** = DOM snapshots per action + network + console + source, viewable as a timeline. Config: `trace: "on-first-retry"` → every CI retry yields a trace artifact.

## Related
- [[Test Isolation and Flakiness]] · [[Playwright CI and Parallelism]]
