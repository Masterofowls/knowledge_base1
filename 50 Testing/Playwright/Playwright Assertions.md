---
tags: [playwright, testing, snippet]
aliases: [web-first assertions, toBeVisible, toHaveText, toHaveScreenshot]
created: 2026-10-26
status: evergreen
---
# Playwright Assertions

> [!summary] TL;DR
> **Web-first assertions** (`await expect(locator).toX()`) retry until the condition holds or the timeout (default 5s) expires. Always `await` them.

```ts
import { test, expect } from "@playwright/test";

await expect(page).toHaveURL(/\/dashboard/);
await expect(page).toHaveTitle("Dashboard · Shop");
await expect(page.getByRole("alert")).toBeVisible();
await expect(page.getByRole("alert")).toHaveText("Saved");
await expect(page.getByTestId("cart-count")).toHaveText("3");
await expect(page.getByRole("listitem")).toHaveCount(5);
await expect(page.getByRole("button", { name: "Submit" })).toBeEnabled();
await expect(page.getByLabel("Email")).toHaveValue("ada@example.com");
await expect(page.getByRole("checkbox")).toBeChecked();
await expect(page.locator(".spinner")).toBeHidden();
await expect(page.getByRole("link")).toHaveAttribute("href", "/pricing");
await expect(page.getByRole("main")).toMatchAriaSnapshot(`
  - heading "Orders" [level=1]
  - table
`);

// Visual regression
await expect(page).toHaveScreenshot("dashboard.png", { maxDiffPixelRatio: 0.01, mask: [page.locator(".clock")] });

// Soft assertions — keep going, report all failures
await expect.soft(page.getByTestId("total")).toHaveText("$30.00");

// Poll arbitrary async code
await expect.poll(async () => (await api.getOrder(id)).status, { timeout: 15_000 }).toBe("shipped");

// Custom timeout / message
await expect(page.getByText("Report ready"), "report should finish").toBeVisible({ timeout: 30_000 });
```

> [!warning] Non-retrying: `expect(await locator.textContent()).toBe("x")` reads once → flaky. Use `await expect(locator).toHaveText("x")`.

## Related
- [[Jest Matchers]] · [[Playwright Locators]] · [[Jest Snapshot Testing]]
