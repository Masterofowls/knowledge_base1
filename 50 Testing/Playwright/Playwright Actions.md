---
tags: [playwright, testing, snippet]
aliases: [click fill press, playwright actions]
created: 2026-10-26
status: evergreen
---
# Playwright Actions

```ts
await page.goto("/login");                         // relative to baseURL
await page.getByLabel("Email").fill("ada@example.com");
await page.getByLabel("Password").fill(process.env.TEST_PASSWORD!);
await page.getByRole("button", { name: "Sign in" }).click();

await page.getByRole("checkbox", { name: "Remember me" }).check();
await page.getByLabel("Country").selectOption("NO");
await page.getByLabel("Search").press("Enter");
await page.keyboard.press("Control+A");
await page.getByText("Item").hover();
await page.getByText("Card").dragTo(page.getByText("Done column"));
await page.getByLabel("Avatar").setInputFiles("fixtures/avatar.png");
await page.getByRole("textbox").pressSequentially("slow typing", { delay: 50 });

// Downloads, popups, dialogs
const downloadPromise = page.waitForEvent("download");
await page.getByRole("link", { name: "Export CSV" }).click();
const download = await downloadPromise;
await download.saveAs("out/report.csv");

const popupPromise = page.waitForEvent("popup");
await page.getByText("Open docs").click();
const popup = await popupPromise;

page.once("dialog", (d) => d.accept());

// Waiting for network as part of an action
const responsePromise = page.waitForResponse((r) => r.url().includes("/api/orders") && r.status() === 201);
await page.getByRole("button", { name: "Place order" }).click();
await responsePromise;
```

> [!warning] Avoid `page.waitForTimeout(…)` — fixed sleeps cause flakiness. Wait for a condition (a locator assertion, a response, a URL).

## Related
- [[Playwright Locators]] · [[Playwright Assertions]] · [[Test Isolation and Flakiness]]
