---
tags: [tutorial, playwright, testing, typescript]
aliases: [Playwright tutorial, E2E tutorial]
created: 2026-10-26
level: intermediate-advanced
time: 3h
---
# Tutorial - E2E Testing with Playwright

> [!abstract] What you'll build
> A professional Playwright suite against the public **TodoMVC demo** (`https://demo.playwright.dev/todomvc`): locators, web-first assertions, Page Object Model, custom fixtures, network mocking, an API-level test, visual comparison, tracing, and CI.

**Requires:** Node 22+. **Notes used:** [[Playwright MOC]].

## Step 1 — Install
```bash
mkdir e2e-todo && cd e2e-todo
npm init playwright@latest -- --quiet --lang=ts --browser=chromium --gha
```
Set `baseURL` in `playwright.config.ts`:
```ts
use: { baseURL: "https://demo.playwright.dev", trace: "on-first-retry", screenshot: "only-on-failure" },
```
```bash
npx playwright test --ui           # keep this open while you work
```

## Step 2 — First test with recorded locators
```bash
npx playwright codegen https://demo.playwright.dev/todomvc
```
Add two todos and tick one while recording, then clean the result into:
```ts
// tests/todo.spec.ts
import { test, expect } from "@playwright/test";

test("add and complete todos", async ({ page }) => {
  await page.goto("/todomvc");
  const input = page.getByPlaceholder("What needs to be done?");
  await input.fill("Buy milk");
  await input.press("Enter");
  await input.fill("Write tests");
  await input.press("Enter");

  const items = page.getByTestId("todo-item");
  await expect(items).toHaveText(["Buy milk", "Write tests"]);

  await items.filter({ hasText: "Buy milk" }).getByRole("checkbox").check();
  await expect(items.filter({ hasText: "Buy milk" })).toHaveClass(/completed/);
  await expect(page.getByTestId("todo-count")).toHaveText("1 item left");
});
```
> [!check] Checkpoint — passes in UI mode. Use the timeline to inspect DOM snapshots before/after each action ([[Playwright Assertions]]).

## Step 3 — Page Object
```ts
// pages/TodoPage.ts
import { expect, type Locator, type Page } from "@playwright/test";

export class TodoPage {
  readonly input: Locator;
  readonly items: Locator;
  readonly count: Locator;

  constructor(readonly page: Page) {
    this.input = page.getByPlaceholder("What needs to be done?");
    this.items = page.getByTestId("todo-item");
    this.count = page.getByTestId("todo-count");
  }

  async goto() { await this.page.goto("/todomvc"); }

  async add(...titles: string[]) {
    for (const t of titles) { await this.input.fill(t); await this.input.press("Enter"); }
  }

  item(title: string) { return this.items.filter({ hasText: title }); }

  async complete(title: string) { await this.item(title).getByRole("checkbox").check(); }

  async remove(title: string) {
    await this.item(title).hover();
    await this.item(title).getByRole("button", { name: "Delete" }).click();
  }

  async filter(name: "All" | "Active" | "Completed") {
    await this.page.getByRole("link", { name }).click();
  }

  async expectTitles(titles: string[]) { await expect(this.items).toHaveText(titles); }
}
```

## Step 4 — Custom fixture
```ts
// fixtures.ts
import { test as base } from "@playwright/test";
import { TodoPage } from "./pages/TodoPage";

export const test = base.extend<{ todoPage: TodoPage }>({
  todoPage: async ({ page }, use) => {
    const todo = new TodoPage(page);
    await todo.goto();
    await use(todo);
  },
});
export { expect } from "@playwright/test";
```
```ts
// tests/filters.spec.ts
import { test, expect } from "../fixtures";

test.describe("filters", () => {
  test.beforeEach(async ({ todoPage }) => {
    await todoPage.add("A", "B", "C");
    await todoPage.complete("B");
  });

  for (const [filter, expected] of [["Active", ["A", "C"]], ["Completed", ["B"]], ["All", ["A", "B", "C"]]] as const) {
    test(`${filter} shows ${expected.join(", ")}`, async ({ todoPage }) => {
      await todoPage.filter(filter);
      await todoPage.expectTitles([...expected]);
    });
  }

  test("deleting removes the item and updates the counter", async ({ todoPage }) => {
    await todoPage.remove("A");
    await todoPage.expectTitles(["B", "C"]);
    await expect(todoPage.count).toHaveText("1 item left");
  });
});
```
> [!info] Each test gets a fresh browser context, so localStorage from one test never leaks into another ([[Test Isolation and Flakiness]]).

## Step 5 — Persistence across reloads
```ts
test("todos persist after reload", async ({ todoPage, page }) => {
  await todoPage.add("Persist me");
  await page.reload();
  await todoPage.expectTitles(["Persist me"]);
  const stored = await page.evaluate(() => localStorage.getItem("react-todos"));
  expect(JSON.parse(stored!)).toEqual([expect.objectContaining({ title: "Persist me" })]);
});
```

## Step 6 — Network mocking & API testing
TodoMVC has no backend, so use a public API to practise:
```ts
// tests/network.spec.ts
import { test, expect } from "@playwright/test";

test("mock a JSON API response in the page", async ({ page }) => {
  await page.route("**/api/users", (route) => route.fulfill({ json: [{ id: 1, name: "Mocked Ada" }] }));
  await page.goto("/todomvc");
  const users = await page.evaluate(() => fetch("/api/users").then((r) => r.json()));
  expect(users[0].name).toBe("Mocked Ada");
});

test("API test with the request fixture", async ({ request }) => {
  const res = await request.post("https://jsonplaceholder.typicode.com/posts", {
    data: { title: "From Playwright", body: "x", userId: 1 },
  });
  expect(res.status()).toBe(201);
  expect(await res.json()).toMatchObject({ title: "From Playwright", id: expect.any(Number) });
});
```
→ [[Playwright Network Mocking]], [[Playwright API Testing]]

## Step 7 — Visual comparison
```ts
test("empty state looks right", async ({ todoPage, page }) => {
  await expect(page).toHaveScreenshot("empty.png");
});
```
First run creates the baseline (and fails — that's expected); re-run passes. Update intentionally with `npx playwright test --update-snapshots`.
> [!warning] Screenshots differ across OSes/fonts — generate baselines in the same Docker image as CI.

## Step 8 — Debug a failure with a trace
Break a test on purpose (expect `"2 items left"`), then:
```bash
npx playwright test --trace on
npx playwright show-report        # click the failed test → Trace
```
Inspect the action log, DOM snapshot, console and network at the moment of failure ([[Playwright Debugging and Trace Viewer]]).

## Step 9 — CI
`--gha` in step 1 created `.github/workflows/playwright.yml`. Push to GitHub and check the uploaded HTML report artifact. Add sharding when the suite grows ([[Playwright CI and Parallelism]]).

## 🏋️ Challenges
1. Add tests for editing a todo by double-clicking (and pressing Escape to cancel).
2. Run the suite on Firefox and WebKit projects; add a mobile project.
3. Test the [[Tutorial - React and Redux Toolkit Task Board|Task Board app]] with `webServer` starting `npm run dev`.
4. Add an accessibility check with `@axe-core/playwright`.
5. Port two tests to Python with [[Playwright Python]].

> [!success]- Solution sketch: challenge 1
> ```ts
> test("edit a todo", async ({ todoPage, page }) => {
>   await todoPage.add("Typo");
>   await todoPage.item("Typo").dblclick();
>   const editor = todoPage.item("Typo").getByRole("textbox", { name: "Edit" });
>   await editor.fill("Fixed");
>   await editor.press("Enter");
>   await todoPage.expectTitles(["Fixed"]);
> });
> ```

## Related
- [[Tutorials MOC]] · [[Playwright Page Object Model]] · [[Playwright Fixtures]]
