---
tags: [tutorial, redux, react, typescript]
aliases: [Redux tutorial, task board tutorial]
created: 2026-10-26
level: intermediate
time: 3-4h
---
# Tutorial - React and Redux Toolkit Task Board

> [!abstract] What you'll build
> A **Kanban-style task board** (To do / Doing / Done) with Redux Toolkit for client state, memoized selectors for filtering, listener middleware for persistence, RTK Query for a mock server, and tests with React Testing Library.

**Prerequisites:** [[Tutorial - React App with Vite and TypeScript]] or equivalent React basics. **Notes used:** [[Redux MOC]], [[Redux Toolkit createSlice]], [[RTK Query]].

## Step 1 — Scaffold
```bash
npm create vite@latest task-board -- --template react-ts
cd task-board
npm i @reduxjs/toolkit react-redux
npm i -D json-server@0.17
npm run dev
```

## Step 2 — The tasks slice
```ts
// src/features/tasks/tasksSlice.ts
import { createSlice, nanoid, type PayloadAction } from "@reduxjs/toolkit";

export type Status = "todo" | "doing" | "done";
export type Task = { id: string; title: string; status: Status; createdAt: string };

type TasksState = { items: Task[]; search: string };
const initialState: TasksState = { items: [], search: "" };

const tasksSlice = createSlice({
  name: "tasks",
  initialState,
  reducers: {
    taskAdded: {
      reducer(state, action: PayloadAction<Task>) { state.items.push(action.payload); },
      prepare(title: string) {
        return { payload: { id: nanoid(), title, status: "todo" as Status, createdAt: new Date().toISOString() } };
      },
    },
    taskMoved(state, action: PayloadAction<{ id: string; status: Status }>) {
      const t = state.items.find(t => t.id === action.payload.id);
      if (t) t.status = action.payload.status;
    },
    taskDeleted(state, action: PayloadAction<string>) {
      state.items = state.items.filter(t => t.id !== action.payload);
    },
    searchChanged(state, action: PayloadAction<string>) { state.search = action.payload; },
  },
});

export const { taskAdded, taskMoved, taskDeleted, searchChanged } = tasksSlice.actions;
export default tasksSlice.reducer;
```

## Step 3 — Store, typed hooks, Provider
```ts
// src/app/store.ts
import { combineReducers, configureStore } from "@reduxjs/toolkit";
import tasks from "../features/tasks/tasksSlice";

const rootReducer = combineReducers({ tasks });
export type RootState = ReturnType<typeof rootReducer>;

export const makeStore = (preloadedState?: Partial<RootState>) =>
  configureStore({ reducer: rootReducer, preloadedState });

export const store = makeStore();
export type AppStore = ReturnType<typeof makeStore>;
export type AppDispatch = AppStore["dispatch"];
```
```ts
// src/app/hooks.ts
import { useDispatch, useSelector } from "react-redux";
import type { AppDispatch, RootState } from "./store";
export const useAppDispatch = useDispatch.withTypes<AppDispatch>();
export const useAppSelector = useSelector.withTypes<RootState>();
```
Wrap `<App />` in `<Provider store={store}>` in `src/main.tsx` ([[Redux Toolkit configureStore]]).

## Step 4 — Memoized selectors
```ts
// src/features/tasks/selectors.ts
import { createSelector } from "@reduxjs/toolkit";
import type { RootState } from "../../app/store";
import type { Status } from "./tasksSlice";

const selectItems = (s: RootState) => s.tasks.items;
const selectSearch = (s: RootState) => s.tasks.search;

export const selectFiltered = createSelector([selectItems, selectSearch], (items, search) =>
  items.filter(t => t.title.toLowerCase().includes(search.toLowerCase())));

export const selectByStatus = createSelector([selectFiltered], (items) => {
  const columns: Record<Status, typeof items> = { todo: [], doing: [], done: [] };
  for (const t of items) columns[t.status].push(t);
  return columns;
});
```

## Step 5 — Components
```tsx
// src/features/tasks/Board.tsx
import { useState } from "react";
import { useAppDispatch, useAppSelector } from "../../app/hooks";
import { searchChanged, taskAdded, taskDeleted, taskMoved, type Status } from "./tasksSlice";
import { selectByStatus } from "./selectors";

const COLUMNS: { status: Status; label: string }[] = [
  { status: "todo", label: "To do" }, { status: "doing", label: "Doing" }, { status: "done", label: "Done" },
];
const NEXT: Record<Status, Status | null> = { todo: "doing", doing: "done", done: null };

export function Board() {
  const dispatch = useAppDispatch();
  const columns = useAppSelector(selectByStatus);
  const search = useAppSelector(s => s.tasks.search);
  const [title, setTitle] = useState("");

  return (
    <main>
      <form onSubmit={(e) => { e.preventDefault(); if (title.trim()) dispatch(taskAdded(title.trim())); setTitle(""); }}>
        <label htmlFor="new-task">New task</label>
        <input id="new-task" value={title} onChange={(e) => setTitle(e.target.value)} />
        <button type="submit">Add</button>
      </form>
      <label htmlFor="search">Search</label>
      <input id="search" value={search} onChange={(e) => dispatch(searchChanged(e.target.value))} />

      <div style={{ display: "grid", gridTemplateColumns: "repeat(3, 1fr)", gap: 16 }}>
        {COLUMNS.map(({ status, label }) => (
          <section key={status} aria-label={label}>
            <h2>{label} ({columns[status].length})</h2>
            <ul>
              {columns[status].map(t => (
                <li key={t.id}>
                  {t.title}
                  {NEXT[status] && (
                    <button onClick={() => dispatch(taskMoved({ id: t.id, status: NEXT[status]! }))}>
                      Move to {NEXT[status]}
                    </button>
                  )}
                  <button aria-label={`Delete ${t.title}`} onClick={() => dispatch(taskDeleted(t.id))}>✕</button>
                </li>
              ))}
            </ul>
          </section>
        ))}
      </div>
    </main>
  );
}
```

> [!check] Checkpoint
> Add three tasks, move one to Done, search for a word. Open **Redux DevTools** → watch each action and the state diff; try time-travelling with the slider ([[Redux Middleware and DevTools]]).

## Step 6 — Persist with listener middleware
```ts
// src/app/persistence.ts
import { createListenerMiddleware, isAnyOf } from "@reduxjs/toolkit";
import { taskAdded, taskDeleted, taskMoved } from "../features/tasks/tasksSlice";
import type { RootState } from "./store";

export const persistence = createListenerMiddleware();
persistence.startListening({
  matcher: isAnyOf(taskAdded, taskMoved, taskDeleted),
  effect: async (_action, api) => {
    api.cancelActiveListeners();
    await api.delay(300);                                   // debounce writes
    localStorage.setItem("tasks", JSON.stringify((api.getState() as RootState).tasks.items));
  },
});

export const loadTasks = () => {
  try { return JSON.parse(localStorage.getItem("tasks") ?? "[]"); } catch { return []; }
};
```
Update `makeStore`: `middleware: (gDM) => gDM().prepend(persistence.middleware)` and `preloadedState ?? { tasks: { items: loadTasks(), search: "" } }`.

> [!check] Checkpoint — reload the page; your tasks survive.

## Step 7 — Server data with RTK Query
Create `db.json` with `{ "projects": [{ "id": 1, "name": "Website" }, { "id": 2, "name": "Mobile app" }] }` and run `npx json-server --watch db.json --port 4000`.

```ts
// src/services/api.ts
import { createApi, fetchBaseQuery } from "@reduxjs/toolkit/query/react";
export type Project = { id: number; name: string };

export const api = createApi({
  reducerPath: "api",
  baseQuery: fetchBaseQuery({ baseUrl: "http://localhost:4000" }),
  tagTypes: ["Project"],
  endpoints: (b) => ({
    getProjects: b.query<Project[], void>({ query: () => "/projects", providesTags: ["Project"] }),
    addProject: b.mutation<Project, { name: string }>({
      query: (body) => ({ url: "/projects", method: "POST", body }),
      invalidatesTags: ["Project"],
    }),
  }),
});
export const { useGetProjectsQuery, useAddProjectMutation } = api;
```
Register `[api.reducerPath]: api.reducer` in `combineReducers` and `.concat(api.middleware)`. Then render a project list with `useGetProjectsQuery()` and an "Add project" button using `useAddProjectMutation()`.

> [!check] Checkpoint — adding a project refetches the list automatically (tag invalidation). Watch the `api/executeQuery` actions in DevTools.

## Step 8 — Tests
```bash
npm i -D vitest jsdom @testing-library/react @testing-library/user-event @testing-library/jest-dom
```
Add to `vite.config.ts`: `test: { environment: "jsdom", globals: true }`. (Vitest uses the same API as Jest — see [[Jest MOC]].)

```tsx
// src/features/tasks/Board.test.tsx
import "@testing-library/jest-dom/vitest";
import { render, screen, within } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import { Provider } from "react-redux";
import { makeStore } from "../../app/store";
import { Board } from "./Board";

function renderBoard() {
  const store = makeStore({ tasks: { items: [], search: "" } });
  render(<Provider store={store}><Board /></Provider>);
  return store;
}

test("adds a task and moves it to Doing", async () => {
  const user = userEvent.setup();
  const store = renderBoard();
  await user.type(screen.getByLabelText("New task"), "Write docs");
  await user.click(screen.getByRole("button", { name: "Add" }));

  const todo = screen.getByRole("region", { name: "To do" });
  expect(within(todo).getByText("Write docs")).toBeInTheDocument();

  await user.click(within(todo).getByRole("button", { name: "Move to doing" }));
  expect(within(screen.getByRole("region", { name: "Doing" })).getByText("Write docs")).toBeInTheDocument();
  expect(store.getState().tasks.items[0].status).toBe("doing");
});
```
```bash
npx vitest
```

## 🏋️ Challenges
1. Convert `items` to a normalized `createEntityAdapter` state ([[Redux Normalized State]]).
2. Add drag-and-drop between columns (`@dnd-kit/core`).
3. Make tasks belong to projects; select the active project from the URL.
4. Add an optimistic update when renaming a project ([[RTK Query]]).
5. Write a Playwright test for the full flow ([[Tutorial - E2E Testing with Playwright]]).

> [!success]- Solution sketch: challenge 1
> ```ts
> const adapter = createEntityAdapter<Task>({ sortComparer: (a, b) => a.createdAt.localeCompare(b.createdAt) });
> const slice = createSlice({
>   name: "tasks",
>   initialState: adapter.getInitialState({ search: "" }),
>   reducers: {
>     taskAdded: { reducer: adapter.addOne, prepare: (title: string) => ({ payload: { id: nanoid(), title, status: "todo" as Status, createdAt: new Date().toISOString() } }) },
>     taskMoved: (s, a: PayloadAction<{ id: string; status: Status }>) => adapter.updateOne(s, { id: a.payload.id, changes: { status: a.payload.status } }),
>     taskDeleted: adapter.removeOne,
>   },
> });
> export const { selectAll: selectAllTasks } = adapter.getSelectors((s: RootState) => s.tasks);
> ```

## Related
- [[Tutorials MOC]] · [[Redux Testing]] · [[Redux vs Context vs Zustand]]
