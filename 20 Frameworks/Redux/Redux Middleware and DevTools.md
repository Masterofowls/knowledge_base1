---
tags: [redux, snippet]
aliases: [listener middleware, redux devtools, time travel]
created: 2026-10-26
status: evergreen
---
# Redux Middleware and DevTools

## Middleware signature
```ts
import type { Middleware } from "@reduxjs/toolkit";

const logger: Middleware = (store) => (next) => (action) => {
  console.groupCollapsed((action as { type: string }).type);
  console.log("prev", store.getState());
  const result = next(action);                    // pass to next middleware / reducer
  console.log("next", store.getState());
  console.groupEnd();
  return result;
};
```

## Listener middleware — side effects in response to actions
```ts
import { createListenerMiddleware, isAnyOf } from "@reduxjs/toolkit";

export const listener = createListenerMiddleware();

listener.startListening({
  matcher: isAnyOf(todoAdded, todoToggled, todoRemoved),
  effect: async (_action, api) => {
    api.cancelActiveListeners();          // debounce
    await api.delay(500);
    localStorage.setItem("todos", JSON.stringify((api.getState() as RootState).todos.items));
  },
});

listener.startListening({
  actionCreator: userLoggedOut,
  effect: (_a, api) => { api.dispatch(apiSlice.util.resetApiState()); },
});

// store: middleware: (gDM) => gDM().prepend(listener.middleware)
```
Replaces most uses of redux-saga / redux-observable.

## Redux DevTools (browser extension)
- Action log with payloads and state diffs.
- **Time travel**: jump/skip actions, replay.
- Export/import state to reproduce bugs.
- Enabled by default in `configureStore`; set `devTools: false` in production if state is sensitive.

## Related
- [[Redux Toolkit configureStore]] · [[Express Middleware]] (same pipeline idea) · [[Redux Testing]]
