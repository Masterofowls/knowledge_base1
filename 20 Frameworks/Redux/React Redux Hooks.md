---
tags: [redux, react, snippet]
aliases: [useSelector, useDispatch, Provider]
created: 2026-10-26
status: evergreen
---
# React Redux Hooks

```tsx
import { useAppDispatch, useAppSelector } from "../../app/hooks";
import { todoAdded, todoToggled, selectVisibleTodos } from "./todosSlice";

export function TodoList() {
  const todos = useAppSelector(selectVisibleTodos);
  const remaining = useAppSelector((s) => s.todos.items.filter(t => !t.done).length);
  const dispatch = useAppDispatch();
  const [text, setText] = useState("");                    // local form state stays local

  return (
    <>
      <form onSubmit={(e) => { e.preventDefault(); dispatch(todoAdded(text)); setText(""); }}>
        <input value={text} onChange={(e) => setText(e.target.value)} aria-label="New todo" />
        <button>Add</button>
      </form>
      <p>{remaining} left</p>
      <ul>
        {todos.map(t => (
          <li key={t.id}>
            <label>
              <input type="checkbox" checked={t.done} onChange={() => dispatch(todoToggled(t.id))} />
              {t.text}
            </label>
          </li>
        ))}
      </ul>
    </>
  );
}
```

## How re-rendering works
- `useSelector` runs after every dispatched action and compares the result with the previous one using **`===`**.
- Component re-renders only if the selected value changed (by reference).

> [!warning] Selector pitfalls
> ```ts
> useAppSelector(s => s.todos.items.filter(t => t.done));   // ❌ new array every time → re-render on ANY action
> useAppSelector(selectDoneTodos);                          // ✅ memoized with createSelector
> useAppSelector(s => ({ a: s.a, b: s.b }));                // ❌ new object; select separately or use shallowEqual
> ```
> See [[Redux Selectors and Reselect]].

Tips:
- Select the **smallest** piece of state each component needs.
- Many small `useSelector` calls are fine and cheap.
- `dispatch` identity is stable — safe in effect deps.

## Related
- [[Redux Toolkit configureStore]] (typed hooks) · [[React Rendering and Reconciliation]] · [[React Context]]
