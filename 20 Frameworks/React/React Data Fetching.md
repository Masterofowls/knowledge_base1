---
tags: [react, concept]
aliases: [TanStack Query, React Query, Suspense, server components]
created: 2026-10-26
status: growing
---
# React Data Fetching

> [!summary] TL;DR
> Hand-rolled `useEffect` fetching misses caching, dedupe, retries, race conditions. Use **TanStack Query** (client) or framework loaders / **Server Components** (Next.js, Remix/React Router).

```jsx
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query";

function Users() {
  const { data, isPending, error } = useQuery({
    queryKey: ["users"],
    queryFn: () => fetch("/api/users").then(r => {
      if (!r.ok) throw new Error(r.statusText);
      return r.json();
    }),
    staleTime: 60_000,
  });

  const qc = useQueryClient();
  const addUser = useMutation({
    mutationFn: (u) => fetch("/api/users", { method: "POST", body: JSON.stringify(u),
                                              headers: { "Content-Type": "application/json" } }),
    onSuccess: () => qc.invalidateQueries({ queryKey: ["users"] }),
  });

  if (isPending) return <Spinner />;
  if (error) return <ErrorBox error={error} />;
  return <UserList users={data} onAdd={addUser.mutate} />;
}
```

## Server Components (RSC)
```jsx
// app/users/page.jsx (Next.js App Router) — runs on the server
export default async function Page() {
  const users = await db.user.findMany();
  return <UserList users={users} />;
}
```

## Related
- [[React useEffect]]
- [[REST API Design]] · [[HTTP Methods]]
- [[Playwright Network Mocking]] — stubbing APIs in E2E
