---
tags: [redux, typescript, snippet]
aliases: [configureStore, store setup, RootState, AppDispatch, typed hooks]
created: 2026-10-26
status: evergreen
---
# Redux Toolkit configureStore

```bash
npm i @reduxjs/toolkit react-redux
```

```ts
// src/app/store.ts
import { configureStore } from "@reduxjs/toolkit";
import todosReducer from "../features/todos/todosSlice";
import filtersReducer from "../features/filters/filtersSlice";
import { api } from "../services/api";

export const store = configureStore({
  reducer: {
    todos: todosReducer,
    filters: filtersReducer,
    [api.reducerPath]: api.reducer,             // RTK Query
  },
  middleware: (getDefault) => getDefault().concat(api.middleware),
  // devTools: process.env.NODE_ENV !== "production"   (default true)
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

```ts
// src/app/hooks.ts — use these everywhere instead of plain useSelector/useDispatch
import { useDispatch, useSelector } from "react-redux";
import type { RootState, AppDispatch } from "./store";

export const useAppDispatch = useDispatch.withTypes<AppDispatch>();
export const useAppSelector = useSelector.withTypes<RootState>();
```

```tsx
// src/main.tsx
import { Provider } from "react-redux";
import { store } from "./app/store";

createRoot(document.getElementById("root")!).render(
  <Provider store={store}>
    <App />
  </Provider>
);
```

What `configureStore` gives you for free: combined reducers, thunk middleware, Redux DevTools, and dev-only checks for **accidental mutation** and **non-serializable values** in state/actions.

> [!tip] For tests and SSR, export a factory: `export const makeStore = (preloadedState?) => configureStore({ reducer, preloadedState })`. See [[Redux Testing]].

## Related
- [[Redux Toolkit createSlice]] · [[React Redux Hooks]] · [[TS Utility Types]] (`ReturnType`)
