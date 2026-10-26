---
tags: [redux, snippet]
aliases: [createSlice, slice, Immer, prepare callback]
created: 2026-10-26
status: evergreen
---
# Redux Toolkit createSlice

> [!summary] TL;DR
> `createSlice` generates a reducer **and** matching action creators from one object. Inside reducers you may "mutate" `state` — Immer turns that into an immutable update.

```ts
// src/features/todos/todosSlice.ts
import { createSlice, nanoid, type PayloadAction } from "@reduxjs/toolkit";

export type Todo = { id: string; text: string; done: boolean };
type TodosState = { items: Todo[]; filter: "all" | "active" | "done" };

const initialState: TodosState = { items: [], filter: "all" };

const todosSlice = createSlice({
  name: "todos",
  initialState,
  reducers: {
    todoAdded: {
      reducer(state, action: PayloadAction<Todo>) {
        state.items.push(action.payload);                 // ✅ Immer: looks mutable, is immutable
      },
      prepare(text: string) {                             // shape the payload, generate ids
        return { payload: { id: nanoid(), text, done: false } };
      },
    },
    todoToggled(state, action: PayloadAction<string>) {
      const todo = state.items.find(t => t.id === action.payload);
      if (todo) todo.done = !todo.done;
    },
    todoRemoved(state, action: PayloadAction<string>) {
      state.items = state.items.filter(t => t.id !== action.payload);   // or return a new value
    },
    filterChanged(state, action: PayloadAction<TodosState["filter"]>) {
      state.filter = action.payload;
    },
    allCleared: () => initialState,                       // returning replaces state
  },
  selectors: {
    selectTodos: (s) => s.items,
    selectFilter: (s) => s.filter,
  },
});

export const { todoAdded, todoToggled, todoRemoved, filterChanged, allCleared } = todosSlice.actions;
export const { selectTodos, selectFilter } = todosSlice.selectors;
export default todosSlice.reducer;
```

```ts
todoAdded("Learn RTK");
// → { type: "todos/todoAdded", payload: { id: "V1St…", text: "Learn RTK", done: false } }
```

## Reacting to other slices' actions
```ts
extraReducers: (builder) => {
  builder
    .addCase(userLoggedOut, () => initialState)
    .addCase(fetchTodos.fulfilled, (state, action) => { state.items = action.payload; });
}
```

> [!warning] Immer rules: **either** mutate `state` **or** return a new value — never both. Don't put non-serializable values (Dates, class instances, Promises) in state; store ISO strings / plain objects.

## Related
- [[Redux Core Concepts]] · [[Redux Async Thunks]] · [[JS Destructuring and Spread]]
