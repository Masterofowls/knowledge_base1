---
tags: [react, concept]
aliases: [keys, rendering lists]
created: 2026-10-26
status: evergreen
---
# React Lists and Keys

```jsx
<ul>
  {todos.map(todo => (
    <TodoItem key={todo.id} todo={todo} />
  ))}
</ul>
```

> [!summary] TL;DR
> `key` tells React **which item is which** between renders so it can reuse DOM & state. It must be **stable, unique among siblings**, and not the array index if the list can reorder/insert/delete.

> [!warning] Index as key bug
> With `key={index}`, deleting the first item shifts every key, so React reuses the wrong component — inputs keep the wrong text, animations glitch.

> [!tip] Reset trick
> Changing a component's `key` remounts it with fresh state: `<Profile key={userId} />`.

## Related
- [[React Rendering and Reconciliation]]
- [[JS Array Methods]]
