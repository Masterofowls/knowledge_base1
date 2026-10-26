---
tags: [redux, concept]
aliases: [createSelector, memoized selectors, reselect]
created: 2026-10-26
status: evergreen
---
# Redux Selectors and Reselect

> [!summary] TL;DR
> Keep state **minimal** and **derive** everything else with selectors. Use `createSelector` to memoize derivations that return new arrays/objects so components don't re-render needlessly.

```ts
import { createSelector } from "@reduxjs/toolkit";
import type { RootState } from "../../app/store";

// Input selectors: plain lookups
const selectItems = (s: RootState) => s.todos.items;
const selectFilter = (s: RootState) => s.todos.filter;

// Memoized output selector: recomputes only when inputs change (===)
export const selectVisibleTodos = createSelector([selectItems, selectFilter], (items, filter) => {
  switch (filter) {
    case "active": return items.filter(t => !t.done);
    case "done":   return items.filter(t => t.done);
    default:       return items;
  }
});

export const selectStats = createSelector([selectItems], (items) => ({
  total: items.length,
  done: items.filter(t => t.done).length,
}));

// Parameterized selector
export const selectTodoById = (s: RootState, id: string) => s.todos.items.find(t => t.id === id);
// usage: useAppSelector(s => selectTodoById(s, id))
```

> [!tip] When a memoized selector takes an argument and is used by many component instances, create one selector per instance with `useMemo(() => makeSelectX(), [])`.

## Related
- [[React Redux Hooks]] · [[React useRef useMemo useCallback]] · [[SQL Aggregation and GROUP BY]] (derived data, same spirit)
