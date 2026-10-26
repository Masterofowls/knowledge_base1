---
tags: [playwright, testing, snippet]
aliases: [sharding, GitHub Actions playwright, workers]
created: 2026-10-26
status: evergreen
---
# Playwright CI and Parallelism

- Test **files** run in parallel across workers; tests within a file run in order unless `fullyParallel: true`.
- Each test gets a fresh **browser context** (cookies, storage isolated) — cheap isolation.
- `test.describe.configure({ mode: "serial" })` for dependent tests; `"parallel"` to parallelize within a file.

## Sharding across machines
```bash
npx playwright test --shard=1/4
npx playwright merge-reports --reporter html ./all-blob-reports
```

## Tags & filtering
```ts
test("checkout @smoke", async ({ page }) => {});
test("refund", { tag: ["@slow", "@payments"] }, async ({ page }) => {});
```
```bash
npx playwright test --grep @smoke
npx playwright test --grep-invert @slow
npx playwright test --project=chromium
```

## GitHub Actions
```yaml
jobs:
  e2e:
    runs-on: ubuntu-latest
    strategy: { matrix: { shard: [1, 2, 3, 4] } }
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 22, cache: npm }
      - run: npm ci
      - run: npx playwright install --with-deps chromium
      - run: npx playwright test --shard=${{ matrix.shard }}/4 --reporter=blob
      - uses: actions/upload-artifact@v4
        if: ${{ !cancelled() }}
        with: { name: blob-report-${{ matrix.shard }}, path: blob-report, retention-days: 7 }
```
Docker alternative: `mcr.microsoft.com/playwright:<version>-noble` image.

## Related
- [[Playwright Setup and Config]] · [[pytest Markers]] · [[Playwright Debugging and Trace Viewer]]
