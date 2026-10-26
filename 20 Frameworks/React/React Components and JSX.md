---
tags: [react, concept]
aliases: [JSX, components]
created: 2026-10-26
status: evergreen
---
# React Components and JSX

> [!summary] TL;DR
> A component is a **function that returns JSX**. JSX is syntax sugar for `React.createElement` calls (via the `react-jsx` transform). Components must be **pure** with respect to props & state during render.

```jsx
export function UserCard({ user, onSelect }) {
  const isAdmin = user.role === "admin";
  return (
    <article className="card" onClick={() => onSelect(user.id)}>
      <h2>{user.name}</h2>
      {isAdmin && <span className="badge">Admin</span>}
      {user.bio ? <p>{user.bio}</p> : <p className="muted">No bio</p>}
    </article>
  );
}
```

## JSX rules
- One root element → use fragments `<>...</>`.
- `className`, `htmlFor`, camelCase events `onClick`.
- `{}` embeds **expressions** (not statements).
- `style={{ marginTop: 8 }}` — object, camelCase.
- Component names start with a **capital letter**.

> [!warning] `{count && <X/>}` renders `0` when count is 0. Use `{count > 0 && <X/>}`.

## Composition with children
```jsx
function Card({ title, children }) {
  return <section><h3>{title}</h3>{children}</section>;
}
<Card title="Stats"><Chart /></Card>
```

## Related
- [[React State and Props]]
- [[React Lists and Keys]]
- [[React Rendering and Reconciliation]]
