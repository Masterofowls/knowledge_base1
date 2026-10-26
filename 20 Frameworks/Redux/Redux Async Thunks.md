---
tags: [redux, snippet]
aliases: [createAsyncThunk, thunks, loading state]
created: 2026-10-26
status: evergreen
---
# Redux Async Thunks

> [!summary] TL;DR
> A **thunk** is a function you dispatch that can run async logic and dispatch more actions. `createAsyncThunk` wraps a promise-returning function and dispatches `pending` / `fulfilled` / `rejected` actions automatically.

```ts
import { createAsyncThunk, createSlice } from "@reduxjs/toolkit";

export const fetchTodos = createAsyncThunk(
  "todos/fetchAll",
  async (_: void, { signal, rejectWithValue }) => {
    const res = await fetch("/api/todos", { signal });
    if (!res.ok) return rejectWithValue(`HTTP ${res.status}`);
    return (await res.json()) as Todo[];
  },
  {
    condition: (_, { getState }) => (getState() as RootState).todos.status !== "loading",  // dedupe
  }
);

type State = { items: Todo[]; status: "idle" | "loading" | "succeeded" | "failed"; error?: string };

const slice = createSlice({
  name: "todos",
  initialState: { items: [], status: "idle" } as State,
  reducers: {},
  extraReducers: (b) => {
    b.addCase(fetchTodos.pending,   (s) => { s.status = "loading"; s.error = undefined; })
     .addCase(fetchTodos.fulfilled, (s, a) => { s.status = "succeeded"; s.items = a.payload; })
     .addCase(fetchTodos.rejected,  (s, a) => { s.status = "failed"; s.error = (a.payload as string) ?? a.error.message; });
  },
});
```

```tsx
function Todos() {
  const dispatch = useAppDispatch();
  const status = useAppSelector(s => s.todos.status);
  useEffect(() => {
    const promise = dispatch(fetchTodos());
    return () => promise.abort();                  // cancels via AbortSignal
  }, [dispatch]);

  // Await the result in a handler:
  const reload = async () => {
    try { await dispatch(fetchTodos()).unwrap(); toast("Reloaded"); }
    catch (err) { toast(`Failed: ${err}`); }
  };
  // ...
}
```

> [!tip] If you're writing lots of thunks + loading flags just to cache server data, switch to [[RTK Query]].

## Related
- [[JS Promises and Async-Await]] · [[TS Unions and Narrowing]] (status union) · [[React useEffect]]
