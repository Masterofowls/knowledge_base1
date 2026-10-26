---
tags: [redux, concept]
aliases: [createEntityAdapter, normalization, ids entities]
created: 2026-10-26
status: evergreen
---
# Redux Normalized State

> [!summary] TL;DR
> Store collections like a database table: `{ ids: [...], entities: { [id]: item } }`. Lookups are O(1), updates touch one place, no duplicated nested copies. `createEntityAdapter` writes the CRUD reducers and selectors for you.

```ts
import { createEntityAdapter, createSlice, type EntityState } from "@reduxjs/toolkit";

type Post = { id: string; title: string; authorId: string; createdAt: string };

const postsAdapter = createEntityAdapter<Post>({
  sortComparer: (a, b) => b.createdAt.localeCompare(a.createdAt),
});

type PostsState = EntityState<Post, string> & { status: "idle" | "loading" };

const postsSlice = createSlice({
  name: "posts",
  initialState: postsAdapter.getInitialState({ status: "idle" }) as PostsState,
  reducers: {
    postAdded: postsAdapter.addOne,
    postUpdated: postsAdapter.updateOne,       // payload: { id, changes }
    postRemoved: postsAdapter.removeOne,
    postsReceived: postsAdapter.setAll,
    postUpserted: postsAdapter.upsertOne,
  },
});

export const {
  selectAll: selectAllPosts,
  selectById: selectPostById,
  selectIds: selectPostIds,
  selectTotal: selectPostCount,
} = postsAdapter.getSelectors((s: RootState) => s.posts);
```

Shape:
```json
{ "ids": ["p2", "p1"], "entities": { "p1": { "id": "p1", "title": "…" }, "p2": { … } }, "status": "idle" }
```

Relationships reference **IDs** (`authorId`), like foreign keys.

> [!tip] Rendering lists: map over `selectPostIds` in the parent and let each row select its own entity by id → only the changed row re-renders.

## Related
- [[SQL Normalization]] — same principle · [[Redux Selectors and Reselect]]
