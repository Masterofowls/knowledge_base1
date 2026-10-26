---
tags: [playwright, testing, concept]
aliases: [test.extend, custom fixtures playwright]
created: 2026-10-26
status: evergreen
---
# Playwright Fixtures

> [!summary] TL;DR
> Like [[pytest Fixtures]]: tests declare what they need by destructuring (`{ page, request }`); Playwright sets it up, isolates it per test, and tears it down.

Built-in: `page`, `context`, `browser`, `browserName`, `request`, `baseURL`, `testInfo` (2nd arg).

```ts
// e2e/fixtures.ts
import { test as base, expect } from "@playwright/test";
import { LoginPage } from "./pages/LoginPage";
import { TodoPage } from "./pages/TodoPage";

type Fixtures = {
  loginPage: LoginPage;
  todoPage: TodoPage;
  seededUser: { email: string; password: string };
};

export const test = base.extend<Fixtures>({
  seededUser: async ({ request }, use) => {
    const user = { email: `u${Date.now()}@test.io`, password: "Passw0rd!" };
    await request.post("/api/test/users", { data: user });       // setup via API = fast
    await use(user);
    await request.delete(`/api/test/users/${user.email}`);       // teardown
  },
  loginPage: async ({ page }, use) => { await use(new LoginPage(page)); },
  todoPage: async ({ page }, use) => {
    const todo = new TodoPage(page);
    await todo.goto();
    await use(todo);
  },
});
export { expect };
```

```ts
import { test, expect } from "./fixtures";
test("user can log in", async ({ loginPage, seededUser, page }) => {
  await loginPage.login(seededUser.email, seededUser.password);
  await expect(page).toHaveURL("/dashboard");
});
```

Options: `{ scope: "worker" }` for expensive shared resources, `{ auto: true }` to run for every test, option fixtures (`[default, { option: true }]`) configurable per project.

## Related
- [[Playwright Page Object Model]] · [[pytest Fixtures]] · [[Jest Setup and Teardown]]
