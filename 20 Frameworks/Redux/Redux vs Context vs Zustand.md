---
tags: [redux, react, comparison]
aliases: [state management comparison, do I need redux]
created: 2026-10-26
status: evergreen
---
# Redux vs Context vs Zustand

| Need | Best fit |
|---|---|
| State used by one component | `useState` / `useReducer` |
| A few rarely-changing globals (theme, locale, current user) | [[React Context]] |
| Server data (fetch, cache, refetch) | [[RTK Query]] or TanStack Query ([[React Data Fetching]]) |
| Lots of shared, frequently-updated client state with complex update logic | **Redux Toolkit** |
| Same, but want minimal boilerplate | **Zustand** / Jotai |
| URL-shareable state (filters, tabs, pagination) | the URL (search params) |
| Form state | React Hook Form / local state |

| | Context | Redux Toolkit | Zustand |
|---|---|---|---|
| Re-render granularity | all consumers on value change | per selector | per selector |
| DevTools / time travel | ❌ | ✅ | ✅ (devtools middleware) |
| Middleware / side-effect patterns | DIY | thunks, listeners, RTK Query | middleware |
| Boilerplate | low | medium | very low |
| Conventions for big teams | weak | strong | medium |

```ts
// Zustand for comparison
import { create } from "zustand";
const useCart = create<{ items: Item[]; add: (i: Item) => void }>()((set) => ({
  items: [],
  add: (i) => set((s) => ({ items: [...s.items, i] })),
}));
const count = useCart((s) => s.items.length);
```

> [!tip] Rule of thumb: start with local state + a server-cache library. Reach for Redux when many features read/write the same client state and you want predictable, traceable updates.

## Related
- [[Redux MOC]] · [[React Context]] · [[React State and Props]]
