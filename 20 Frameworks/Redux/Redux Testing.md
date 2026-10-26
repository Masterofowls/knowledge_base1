---
tags: [redux, testing, jest, snippet]
aliases: [testing redux, renderWithProviders]
created: 2026-10-26
status: evergreen
---
# Redux Testing

> [!summary] TL;DR
> Prefer **integration tests**: render components with a real store and mock only the network. Reducers and selectors are pure functions — trivial to unit-test when logic is complex.

## Reducer & selector unit tests
```ts
import reducer, { todoAdded, todoToggled } from "./todosSlice";

test("adds and toggles a todo", () => {
  let state = reducer(undefined, { type: "@@INIT" });
  state = reducer(state, todoAdded("Write tests"));
  const id = state.items[0].id;
  state = reducer(state, todoToggled(id));
  expect(state.items[0]).toMatchObject({ text: "Write tests", done: true });
});

test("selectVisibleTodos filters done", () => {
  const state = { todos: { items: [{ id: "1", text: "a", done: true }, { id: "2", text: "b", done: false }], filter: "done" } };
  expect(selectVisibleTodos(state as RootState)).toHaveLength(1);
});
```

## Component tests with a real store
```tsx
// test-utils.tsx
import { render } from "@testing-library/react";
import { Provider } from "react-redux";
import { makeStore, type RootState } from "../app/store";

export function renderWithProviders(ui: React.ReactElement, { preloadedState }: { preloadedState?: Partial<RootState> } = {}) {
  const store = makeStore(preloadedState);
  return { store, ...render(<Provider store={store}>{ui}</Provider>) };
}
```
```tsx
test("user adds a todo", async () => {
  const { store } = renderWithProviders(<TodoList />);
  await userEvent.type(screen.getByLabelText("New todo"), "Buy milk");
  await userEvent.click(screen.getByRole("button", { name: "Add" }));
  expect(screen.getByText("Buy milk")).toBeInTheDocument();
  expect(store.getState().todos.items).toHaveLength(1);
});
```

## Thunks / RTK Query
Mock HTTP with **MSW** ([[Jest Module Mocking]]) and assert on the rendered UI or `store.getState()` — don't mock `dispatch`.

## Related
- [[Jest Testing React Components]] · [[Testing Pyramid]] · [[Redux Toolkit configureStore]] (`makeStore`)
