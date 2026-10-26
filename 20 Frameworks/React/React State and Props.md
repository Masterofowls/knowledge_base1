---
tags: [react, concept]
aliases: [useState, props, lifting state up]
created: 2026-10-26
status: evergreen
---
# React State and Props

> [!summary] TL;DR
> **Props** flow down (read-only inputs). **State** is component memory; updating it schedules a re-render. Data flows down, events flow up.

```jsx
function Counter({ step = 1 }) {
  const [count, setCount] = useState(0);
  return (
    <button onClick={() => setCount(c => c + step)}>   {/* updater fn */}
      {count}
    </button>
  );
}
```

## Rules of state
1. **Never mutate** — create new objects/arrays ([[JS Destructuring and Spread]]).
2. State updates are **batched & async**; `count` doesn't change until next render.
3. Use the **updater form** `setX(prev => ...)` when new state depends on old.
4. Lazy init for expensive defaults: `useState(() => compute())`.
5. Don't store what you can **derive** during render.

```jsx
// ❌ redundant state + effect
const [fullName, setFullName] = useState("");
useEffect(() => setFullName(`${first} ${last}`), [first, last]);
// ✅ derive it
const fullName = `${first} ${last}`;
```

## Lifting state up
When siblings need the same data, move state to their closest common parent and pass it down plus a callback.

## useReducer for complex state
```jsx
function reducer(state, action) {
  switch (action.type) {
    case "add": return { ...state, items: [...state.items, action.item] };
    case "remove": return { ...state, items: state.items.filter(i => i.id !== action.id) };
    default: throw new Error("unknown action");
  }
}
const [state, dispatch] = useReducer(reducer, { items: [] });
```

## Related
- [[React Context]] — avoid prop drilling
- [[React Rendering and Reconciliation]]
- [[React Forms]]
