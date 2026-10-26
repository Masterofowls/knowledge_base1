---
tags: [redux, react, snippet]
aliases: [createApi, fetchBaseQuery, RTKQ, cache tags]
created: 2026-10-26
status: evergreen
---
# RTK Query

> [!summary] TL;DR
> RTK Query defines an API once and generates **hooks** that fetch, cache, dedupe, refetch and invalidate server data — no thunks, no loading flags, no reducers.

```ts
// src/services/api.ts
import { createApi, fetchBaseQuery } from "@reduxjs/toolkit/query/react";
import type { RootState } from "../app/store";

export const api = createApi({
  reducerPath: "api",
  baseQuery: fetchBaseQuery({
    baseUrl: "/api",
    prepareHeaders: (headers, { getState }) => {
      const token = (getState() as RootState).auth.token;
      if (token) headers.set("Authorization", `Bearer ${token}`);
      return headers;
    },
  }),
  tagTypes: ["Todo"],
  endpoints: (build) => ({
    getTodos: build.query<Todo[], { done?: boolean } | void>({
      query: (params) => ({ url: "/todos", params: params ?? undefined }),
      providesTags: (result) =>
        result ? [...result.map(t => ({ type: "Todo" as const, id: t.id })), { type: "Todo", id: "LIST" }]
               : [{ type: "Todo", id: "LIST" }],
    }),
    getTodo: build.query<Todo, string>({
      query: (id) => `/todos/${id}`,
      providesTags: (_r, _e, id) => [{ type: "Todo", id }],
    }),
    addTodo: build.mutation<Todo, { text: string }>({
      query: (body) => ({ url: "/todos", method: "POST", body }),
      invalidatesTags: [{ type: "Todo", id: "LIST" }],          // → getTodos refetches
    }),
    updateTodo: build.mutation<Todo, Pick<Todo, "id"> & Partial<Todo>>({
      query: ({ id, ...patch }) => ({ url: `/todos/${id}`, method: "PATCH", body: patch }),
      invalidatesTags: (_r, _e, { id }) => [{ type: "Todo", id }],
      // Optimistic update
      async onQueryStarted({ id, ...patch }, { dispatch, queryFulfilled }) {
        const undo = dispatch(api.util.updateQueryData("getTodos", undefined, (draft) => {
          const t = draft.find(t => t.id === id);
          if (t) Object.assign(t, patch);
        }));
        try { await queryFulfilled; } catch { undo.undo(); }
      },
    }),
  }),
});

export const { useGetTodosQuery, useGetTodoQuery, useAddTodoMutation, useUpdateTodoMutation } = api;
```

```tsx
function TodoPage() {
  const { data: todos = [], isLoading, isFetching, error, refetch } = useGetTodosQuery(undefined, {
    pollingInterval: 30_000,
    refetchOnFocus: true,                     // needs setupListeners(store.dispatch)
  });
  const [addTodo, { isLoading: adding }] = useAddTodoMutation();

  if (isLoading) return <Spinner />;
  if (error) return <ErrorBox error={error} />;
  return (
    <>
      <button disabled={adding} onClick={() => addTodo({ text: "New" }).unwrap()}>Add</button>
      {isFetching && <small>Refreshing…</small>}
      <TodoList todos={todos} />
    </>
  );
}
```

Extras: `skip` / `skipToken` for conditional queries, `useLazyQuery`, `transformResponse`, code-generation from OpenAPI (`@rtk-query/codegen-openapi` → [[OpenAPI Tooling]]).

## Related
- [[React Data Fetching]] (TanStack Query equivalent) · [[REST API Design]] · [[Redux Async Thunks]]
