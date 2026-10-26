---
tags: [playwright, guide]
aliases: [Learn Playwright, Playwright roadmap]
created: 2026-10-26
---
# Playwright Getting Started

## 1. Install
```bash
npm init playwright@latest        # choose TypeScript, tests folder, GitHub Actions
npx playwright install --with-deps
```
VS Code: install the **Playwright Test for VS Code** extension (run/debug tests, pick locators, record).

## 2. First test
```ts
// tests/example.spec.ts
import { test, expect } from "@playwright/test";

test("homepage has a docs link", async ({ page }) => {
  await page.goto("https://playwright.dev/");
  await expect(page).toHaveTitle(/Playwright/);
  await page.getByRole("link", { name: "Get started" }).click();
  await expect(page.getByRole("heading", { name: "Installation" })).toBeVisible();
});
```
```bash
npx playwright test
npx playwright test --ui
npx playwright show-report
```

## 3. Learning checklist
- [ ] Config & projects → [[Playwright Setup and Config]]
- [ ] Locators → [[Playwright Locators]]
- [ ] Actions → [[Playwright Actions]]
- [ ] Web-first assertions → [[Playwright Assertions]]
- [ ] Fixtures → [[Playwright Fixtures]]
- [ ] Page Object Model → [[Playwright Page Object Model]]
- [ ] Authentication & storageState → [[Playwright Authentication]]
- [ ] Network mocking → [[Playwright Network Mocking]]
- [ ] API testing → [[Playwright API Testing]]
- [ ] Debugging & traces → [[Playwright Debugging and Trace Viewer]]
- [ ] CI, sharding, tags → [[Playwright CI and Parallelism]]
- [ ] Python flavour → [[Playwright Python]]
- [ ] Practise: [[Tutorial - E2E Testing with Playwright]]

## 4. Beginner mistakes
- CSS/XPath selectors tied to layout instead of `getByRole`/`getByLabel`.
- `waitForTimeout` sleeps instead of web-first assertions.
- Reading values once (`textContent()`) then asserting — no auto-retry.
- Logging in through the UI in every test (use `storageState`).
- Tests sharing data and failing in parallel.

## Resources
- playwright.dev/docs/intro · "Best Practices" page
- Playwright YouTube channel — release videos show new features
