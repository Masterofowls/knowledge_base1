---
tags: [react, concept]
aliases: [useEffect, effects, stale closure]
created: 2026-10-26
status: evergreen
---
# React useEffect

> [!summary] TL;DR
> `useEffect` **synchronises a component with something outside React** (network, DOM APIs, subscriptions, timers). It runs *after* paint. If no external system is involved, you probably don't need an effect.

```jsx
useEffect(() => {
  const ctrl = new AbortController();
  fetch(`/api/users/${id}`, { signal: ctrl.signal })
    .then(r => r.json())
    .then(setUser)
    .catch(e => { if (e.name !== "AbortError") setError(e); });

  return () => ctrl.abort();            // cleanup: before re-run & on unmount
}, [id]);                               // re-run when id changes
```

## Dependency array
| Form | Runs |
|---|---|
| no array | after every render |
| `[]` | after mount only (+ cleanup on unmount) |
| `[a, b]` | after mount and whenever `a` or `b` changes (`Object.is`) |

> [!warning] Stale closures
> ```jsx
> useEffect(() => {
>   const t = setInterval(() => setCount(count + 1), 1000); // always sees initial count
>   return () => clearInterval(t);
> }, []);
> // ✅ setCount(c => c + 1)
> ```
> See [[JS Functions and Closures]].

## You might not need an effect
- Deriving data → compute during render (or [[React useRef useMemo useCallback|useMemo]]).
- Reacting to a user event → do it in the event handler.
- Resetting state on prop change → use `key`.
- Fetching data → prefer a library ([[React Data Fetching]]).

> [!info] StrictMode in dev mounts → unmounts → mounts again to surface missing cleanups.

## Related
- [[React Custom Hooks]]
- [[JS Event Loop]]
