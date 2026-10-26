---
tags: [react, typescript, snippet]
aliases: [React TS, typing props]
created: 2026-10-26
status: evergreen
---
# React with TypeScript

```tsx
type ButtonProps = {
  variant?: "primary" | "ghost";
  onClick?: (e: React.MouseEvent<HTMLButtonElement>) => void;
  children: React.ReactNode;
} & React.ComponentPropsWithoutRef<"button">;   // inherit native props

export function Button({ variant = "primary", children, ...rest }: ButtonProps) {
  return <button className={variant} {...rest}>{children}</button>;
}

// State
const [user, setUser] = useState<User | null>(null);
const inputRef = useRef<HTMLInputElement>(null);

// Events
const onChange = (e: React.ChangeEvent<HTMLInputElement>) => setQ(e.target.value);

// Generic component
type ListProps<T> = { items: T[]; render: (item: T) => React.ReactNode; getKey: (t: T) => string };
export function List<T>({ items, render, getKey }: ListProps<T>) {
  return <ul>{items.map(i => <li key={getKey(i)}>{render(i)}</li>)}</ul>;
}

// Reducer with discriminated union actions
type Action = { type: "add"; item: Todo } | { type: "remove"; id: string };
```

> [!tip] Model async UI state as a discriminated union (`idle | loading | success | error`) — see [[TS Unions and Narrowing]].

## Related
- [[TypeScript MOC]] · [[React MOC]]
