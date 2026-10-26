---
tags: [react, snippet]
aliases: [custom hooks, rules of hooks]
created: 2026-10-26
status: evergreen
---
# React Custom Hooks

> [!summary] TL;DR
> A custom hook is a function named `useX` that calls other hooks. It shares **logic**, not state — each caller gets its own state.

## Rules of hooks
1. Call hooks only at the **top level** (not in conditions/loops).
2. Call them only from components or other hooks.

```jsx
export function useLocalStorage(key, initial) {
  const [value, setValue] = useState(() => {
    try { return JSON.parse(localStorage.getItem(key)) ?? initial; }
    catch { return initial; }
  });
  useEffect(() => { localStorage.setItem(key, JSON.stringify(value)); }, [key, value]);
  return [value, setValue];
}

export function useDebounce(value, ms = 300) {
  const [debounced, setDebounced] = useState(value);
  useEffect(() => {
    const t = setTimeout(() => setDebounced(value), ms);
    return () => clearTimeout(t);
  }, [value, ms]);
  return debounced;
}
```

## Testing hooks
```js
import { renderHook, act } from "@testing-library/react";
const { result } = renderHook(() => useCounter());
act(() => result.current.inc());
expect(result.current.count).toBe(1);
```
See [[Jest Testing React Components]].

## Related
- [[React useEffect]] · [[React Context]]
