---
tags: [redux, concept]
aliases: [store, actions, reducers, one-way data flow]
created: 2026-10-26
status: evergreen
---
# Redux Core Concepts

> [!summary] TL;DR
> All global state lives in one **store**. The UI **dispatches actions** (plain objects describing *what happened*). **Reducers** — pure functions `(state, action) => newState` — compute the next state. Subscribed components re-render with the new state.

```
      ┌──────────── dispatch(action) ◄────────────┐
      ▼                                            │
 ┌─────────┐    (state, action)    ┌──────────┐    │
 │  Store  │ ───────────────────►  │ Reducer  │    │
 │ (state) │ ◄───── newState ───── └──────────┘    │
 └────┬────┘                                       │
      │ useSelector(state => …)                    │
      ▼                                            │
 ┌─────────┐   user clicks / data arrives         │
 │   UI    │ ─────────────────────────────────────┘
 └─────────┘
```

## Three principles
1. **Single source of truth** — one state tree.
2. **State is read-only** — change it only by dispatching actions.
3. **Changes via pure reducers** — no side effects, no mutation (RTK lets you *write* mutating code via Immer, but the result is a new immutable object).

```js
// An action
{ type: "todos/added", payload: { id: "t1", text: "Learn Redux" } }

// A reducer (vanilla, for understanding)
function todosReducer(state = [], action) {
  switch (action.type) {
    case "todos/added":   return [...state, { ...action.payload, done: false }];
    case "todos/toggled": return state.map(t => t.id === action.payload ? { ...t, done: !t.done } : t);
    default:              return state;
  }
}
```

## Vocabulary
| Term | Meaning |
|---|---|
| store | holds state; `getState()`, `dispatch()`, `subscribe()` |
| action | `{ type, payload }` event |
| action creator | function returning an action |
| reducer | pure `(state, action) => state` |
| slice | reducer + actions for one feature (`state.todos`) |
| selector | function reading/deriving from state |
| thunk | function dispatched to run async logic |
| middleware | intercepts dispatched actions (logging, async) |

## What belongs in Redux?
✅ Global UI/client state shared across distant components (auth user, cart, filters, wizard progress).
⚠️ Server data → prefer [[RTK Query]] (or TanStack Query).
❌ Form input state, hover/open toggles local to one component → `useState`.

## Related
- [[Redux Toolkit createSlice]] · [[React State and Props]] (`useReducer` uses the same idea)
