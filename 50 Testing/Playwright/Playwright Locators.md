---
tags: [playwright, testing, concept]
aliases: [getByRole, locators, selectors]
created: 2026-10-26
status: evergreen
---
# Playwright Locators

> [!summary] TL;DR
> A **Locator** is a lazy, re-evaluated description of how to find element(s). Actions and assertions on it **auto-wait** for the element to be attached, visible, stable, enabled. Prefer user-facing locators.

```ts
// ✅ Recommended (priority order)
page.getByRole("button", { name: "Sign in" });
page.getByRole("heading", { level: 1 });
page.getByLabel("Email");
page.getByPlaceholder("Search…");
page.getByText("Welcome back", { exact: true });
page.getByAltText("Company logo");
page.getByTitle("Close");
page.getByTestId("cart-count");           // data-testid

// CSS / XPath — last resort (brittle)
page.locator("form#login >> input[name=email]");
```

## Chaining & filtering
```ts
const row = page.getByRole("row").filter({ hasText: "Ada Lovelace" });
await row.getByRole("button", { name: "Edit" }).click();

page.getByRole("listitem").filter({ has: page.getByRole("heading", { name: "Pro plan" }) });
page.getByRole("listitem").filter({ hasNot: page.getByText("Sold out") });
page.getByRole("link").nth(2);   .first();   .last();
page.getByRole("button").and(page.getByTitle("Subscribe"));
page.getByRole("button", { name: "Save" }).or(page.getByRole("button", { name: "Update" }));
page.frameLocator("#payment").getByLabel("Card number");
```

## Strictness
Actions on a locator matching **multiple** elements throw (strict mode) — make it specific instead of grabbing `.first()` blindly.

> [!tip] `npx playwright codegen` and the UI mode "Pick locator" button suggest good locators. Accessible markup = easy, stable locators.

## Related
- [[Playwright Actions]] · [[Playwright Assertions]] · [[Jest Testing React Components]] (same query philosophy)
