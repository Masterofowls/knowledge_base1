---
tags: [playwright, testing, concept]
aliases: [POM, page objects]
created: 2026-10-26
status: evergreen
---
# Playwright Page Object Model

> [!summary] TL;DR
> A Page Object wraps a page's locators and user-level actions behind a class, so tests read like user stories and selector changes happen in one place.

```ts
// e2e/pages/LoginPage.ts
import { type Page, type Locator, expect } from "@playwright/test";

export class LoginPage {
  readonly email: Locator;
  readonly password: Locator;
  readonly submit: Locator;
  readonly error: Locator;

  constructor(private readonly page: Page) {
    this.email = page.getByLabel("Email");
    this.password = page.getByLabel("Password");
    this.submit = page.getByRole("button", { name: "Sign in" });
    this.error = page.getByRole("alert");
  }

  async goto() { await this.page.goto("/login"); }

  async login(email: string, password: string) {
    await this.goto();
    await this.email.fill(email);
    await this.password.fill(password);
    await this.submit.click();
  }

  async expectError(message: string | RegExp) {
    await expect(this.error).toHaveText(message);
  }
}
```

```ts
test("rejects bad password", async ({ loginPage }) => {
  await loginPage.login("ada@example.com", "wrong");
  await loginPage.expectError(/invalid credentials/i);
});
```

Guidelines:
- Methods = **user intentions** (`addToCart`), not low-level clicks.
- Keep most **assertions in tests**; helper `expect*` methods are fine for reuse.
- Compose components (`Header`, `CartDrawer`) rather than giant page classes.
- Inject via [[Playwright Fixtures]], not `new` in every test.

## Related
- [[JS Prototypes and Classes]] · [[Playwright Locators]]
