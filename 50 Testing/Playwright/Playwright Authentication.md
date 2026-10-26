---
tags: [playwright, testing, snippet]
aliases: [storageState, auth setup project, login once]
created: 2026-10-26
status: evergreen
---
# Playwright Authentication

> [!summary] TL;DR
> Log in **once** in a setup project, save cookies/localStorage to a file with `storageState`, and reuse it in every test context.

```ts
// e2e/auth.setup.ts
import { test as setup, expect } from "@playwright/test";
const authFile = ".auth/user.json";

setup("authenticate", async ({ page }) => {
  await page.goto("/login");
  await page.getByLabel("Email").fill(process.env.E2E_USER!);
  await page.getByLabel("Password").fill(process.env.E2E_PASSWORD!);
  await page.getByRole("button", { name: "Sign in" }).click();
  await expect(page).toHaveURL("/dashboard");
  await page.context().storageState({ path: authFile });
});

// Faster: authenticate via API
setup("authenticate via API", async ({ request }) => {
  await request.post("/api/auth/login", { data: { email: process.env.E2E_USER, password: process.env.E2E_PASSWORD } });
  await request.storageState({ path: authFile });
});
```

Wire it up in [[Playwright Setup and Config]]: setup project + `dependencies: ["setup"]` + `use.storageState`.

```ts
// Multiple roles in one test
test("admin sees user's ticket", async ({ browser }) => {
  const admin = await browser.newContext({ storageState: ".auth/admin.json" });
  const user  = await browser.newContext({ storageState: ".auth/user.json" });
  // ...
});

// Opt out for a file
test.use({ storageState: { cookies: [], origins: [] } });
```

> [!warning] Add `.auth/` to `.gitignore`. Use dedicated test accounts and keep credentials in CI secrets / `.env`, never in code.

## Related
- [[Authentication Schemes]] · [[Playwright Fixtures]]
