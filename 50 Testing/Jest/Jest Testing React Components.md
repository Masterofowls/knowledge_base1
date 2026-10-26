---
tags: [jest, react, testing, snippet]
aliases: [React Testing Library, RTL, testing-library]
created: 2026-10-26
status: evergreen
---
# Jest Testing React Components

> [!summary] TL;DR
> React Testing Library: render a component, interact **like a user**, assert on what the user sees. Query by role/label/text, not by CSS classes or component internals.

```tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import "@testing-library/jest-dom";
import { LoginForm } from "./LoginForm";

test("submits credentials", async () => {
  const user = userEvent.setup();
  const onSubmit = jest.fn();
  render(<LoginForm onSubmit={onSubmit} />);

  await user.type(screen.getByLabelText(/email/i), "ada@example.com");
  await user.type(screen.getByLabelText(/password/i), "secret123");
  await user.click(screen.getByRole("button", { name: /log in/i }));

  expect(onSubmit).toHaveBeenCalledWith({ email: "ada@example.com", password: "secret123" });
});

test("shows error from server", async () => {
  render(<LoginForm onSubmit={jest.fn().mockRejectedValue(new Error("Invalid"))} />);
  await userEvent.click(screen.getByRole("button", { name: /log in/i }));
  expect(await screen.findByRole("alert")).toHaveTextContent("Invalid");   // findBy = waits
});
```

## Query priority
1. `getByRole` (with `name`) — best, mirrors accessibility tree
2. `getByLabelText`, `getByPlaceholderText`, `getByText`, `getByDisplayValue`
3. `getByAltText`, `getByTitle`
4. `getByTestId` — last resort

| Prefix | 0 matches | >1 | Async |
|---|---|---|---|
| `getBy` | throw | throw | no |
| `queryBy` | `null` (assert absence) | throw | no |
| `findBy` | reject | reject | yes (waits) |
| `*AllBy` | arrays | | |

jest-dom matchers: `toBeInTheDocument`, `toBeVisible`, `toBeDisabled`, `toHaveValue`, `toHaveTextContent`, `toHaveAttribute`.

> [!tip] Same locator philosophy as [[Playwright Locators]] — skills transfer directly.

## Related
- [[React Forms]] · [[React Custom Hooks]] (`renderHook`) · [[Jest Module Mocking]] (MSW)
