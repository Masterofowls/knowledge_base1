---
tags: [playwright, testing, snippet]
aliases: [playwright.config, npm init playwright]
created: 2026-10-26
status: evergreen
---
# Playwright Setup and Config

```bash
npm init playwright@latest          # scaffolds config, example tests, GH Actions workflow
npx playwright install --with-deps  # browsers + OS deps
npx playwright test                 # run
npx playwright test --ui            # UI mode (watch, time-travel)
npx playwright codegen localhost:3000   # record locators/actions
npx playwright show-report
```

```ts
// playwright.config.ts
import { defineConfig, devices } from "@playwright/test";

export default defineConfig({
  testDir: "./e2e",
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 4 : undefined,
  reporter: [["html", { open: "never" }], ["list"]],
  use: {
    baseURL: process.env.BASE_URL ?? "http://localhost:3000",
    trace: "on-first-retry",
    screenshot: "only-on-failure",
    video: "retain-on-failure",
    testIdAttribute: "data-testid",
  },
  projects: [
    { name: "setup", testMatch: /.*\.setup\.ts/ },
    { name: "chromium", use: { ...devices["Desktop Chrome"], storageState: ".auth/user.json" }, dependencies: ["setup"] },
    { name: "firefox",  use: { ...devices["Desktop Firefox"], storageState: ".auth/user.json" }, dependencies: ["setup"] },
    { name: "mobile",   use: { ...devices["Pixel 7"] } },
    { name: "api", testDir: "./api-tests", use: { baseURL: "http://localhost:8000" } },
  ],
  webServer: {
    command: "npm run dev",
    url: "http://localhost:3000",
    reuseExistingServer: !process.env.CI,
    timeout: 120_000,
  },
});
```

## Related
- [[Playwright Authentication]] · [[Playwright CI and Parallelism]]
