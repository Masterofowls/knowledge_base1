---
tags: [react, concept]
aliases: [useRef, useMemo, useCallback, memoization]
created: 2026-10-26
status: evergreen
---
# React useRef useMemo useCallback

## useRef — mutable box that survives renders, no re-render on change
```jsx
const inputRef = useRef(null);
<input ref={inputRef} />;
inputRef.current.focus();

const renders = useRef(0);
renders.current++;               // doesn't trigger re-render
```
Use for DOM nodes, timer IDs, previous values, instance-like data.

## useMemo — cache a computed value
```jsx
const visible = useMemo(
  () => todos.filter(t => matches(t, filter)),
  [todos, filter]
);
```

## useCallback — cache a function identity
```jsx
const handleSelect = useCallback((id) => setSelected(id), []);
<ExpensiveList onSelect={handleSelect} />   // with React.memo(ExpensiveList)
```
`useCallback(fn, deps)` ≡ `useMemo(() => fn, deps)`.

> [!tip] When to memoize
> Only when (1) a computation is measurably slow, or (2) referential stability matters (prop to a `memo` component, or a dependency of another hook). The **React Compiler** auto-memoizes, making most manual memo unnecessary.

## Related
- [[React Rendering and Reconciliation]]
- [[JS Functions and Closures]] — memo pattern
