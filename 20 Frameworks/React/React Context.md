---
tags: [react, concept]
aliases: [useContext, context, prop drilling]
created: 2026-10-26
status: evergreen
---
# React Context

> [!summary] TL;DR
> Context passes a value deep into the tree without prop drilling. Great for **low-frequency global data**: theme, auth user, locale. Not a state manager by itself.

```jsx
const AuthContext = createContext(null);

export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const value = useMemo(() => ({ user, login: setUser, logout: () => setUser(null) }), [user]);
  return <AuthContext value={value}>{children}</AuthContext>;   // React 19; earlier: <AuthContext.Provider>
}

export function useAuth() {
  const ctx = useContext(AuthContext);
  if (!ctx) throw new Error("useAuth must be used inside <AuthProvider>");
  return ctx;
}
```

> [!warning] Every consumer re-renders when the value changes (by identity). Memoize the value; split contexts by update frequency; use Zustand/Redux/Jotai for high-frequency shared state.

## Related
- [[React Custom Hooks]]
- [[React Rendering and Reconciliation]]
- [[Authentication Schemes]]
