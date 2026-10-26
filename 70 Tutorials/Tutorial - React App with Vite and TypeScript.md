---
tags: [tutorial, react, typescript, jest]
aliases: [React tutorial, Vite React tutorial]
created: 2026-10-26
level: intermediate
time: 3h
---
# Tutorial - React App with Vite and TypeScript

> [!abstract] What you'll build
> A **GitHub repo explorer**: search a user, list their repos with sorting and filtering, view details. Covers components, props, state, effects, custom hooks, data fetching with cancellation, and component tests.

**Prerequisites:** [[JavaScript MOC|JavaScript]] basics, some [[TypeScript MOC|TypeScript]]. **Notes used:** [[React MOC]].

## Step 1 — Scaffold
```bash
npm create vite@latest repo-explorer -- --template react-ts
cd repo-explorer && npm i && npm run dev
```
Delete the demo content in `App.tsx` and `App.css`.

## Step 2 — Types and a fetch helper
```ts
// src/api.ts
export type Repo = {
  id: number; name: string; html_url: string; description: string | null;
  stargazers_count: number; language: string | null; updated_at: string;
};

export async function fetchRepos(user: string, signal?: AbortSignal): Promise<Repo[]> {
  const res = await fetch(`https://api.github.com/users/${encodeURIComponent(user)}/repos?per_page=100`, { signal });
  if (res.status === 404) throw new Error(`User "${user}" not found`);
  if (!res.ok) throw new Error(`GitHub error ${res.status}`);
  return res.json();
}
```

## Step 3 — A data-fetching custom hook
```ts
// src/useRepos.ts
import { useEffect, useState } from "react";
import { fetchRepos, type Repo } from "./api";

type State =
  | { status: "idle" }
  | { status: "loading" }
  | { status: "success"; repos: Repo[] }
  | { status: "error"; error: string };

export function useRepos(user: string): State {
  const [state, setState] = useState<State>({ status: "idle" });

  useEffect(() => {
    if (!user) { setState({ status: "idle" }); return; }
    const ctrl = new AbortController();
    setState({ status: "loading" });
    fetchRepos(user, ctrl.signal)
      .then(repos => setState({ status: "success", repos }))
      .catch(e => { if (e.name !== "AbortError") setState({ status: "error", error: e.message }); });
    return () => ctrl.abort();             // cancel stale requests when user changes
  }, [user]);

  return state;
}
```
> [!info] This is a discriminated union ([[TS Unions and Narrowing]]) — TypeScript knows `repos` only exists when `status === "success"`.

## Step 4 — Components
```tsx
// src/SearchForm.tsx
import { useState } from "react";
export function SearchForm({ onSearch }: { onSearch: (user: string) => void }) {
  const [value, setValue] = useState("");
  return (
    <form onSubmit={(e) => { e.preventDefault(); onSearch(value.trim()); }}>
      <label htmlFor="user">GitHub user</label>
      <input id="user" value={value} onChange={(e) => setValue(e.target.value)} placeholder="e.g. gaearon" />
      <button type="submit">Search</button>
    </form>
  );
}
```
```tsx
// src/RepoList.tsx
import { useMemo, useState } from "react";
import type { Repo } from "./api";

type Sort = "stars" | "updated" | "name";

export function RepoList({ repos }: { repos: Repo[] }) {
  const [sort, setSort] = useState<Sort>("stars");
  const [language, setLanguage] = useState("all");

  const languages = useMemo(
    () => [...new Set(repos.map(r => r.language).filter(Boolean))].sort() as string[], [repos]);

  const visible = useMemo(() => {
    const filtered = language === "all" ? repos : repos.filter(r => r.language === language);
    const cmp: Record<Sort, (a: Repo, b: Repo) => number> = {
      stars: (a, b) => b.stargazers_count - a.stargazers_count,
      updated: (a, b) => b.updated_at.localeCompare(a.updated_at),
      name: (a, b) => a.name.localeCompare(b.name),
    };
    return filtered.toSorted(cmp[sort]);
  }, [repos, sort, language]);

  return (
    <>
      <label>Sort <select value={sort} onChange={(e) => setSort(e.target.value as Sort)}>
        <option value="stars">Stars</option><option value="updated">Updated</option><option value="name">Name</option>
      </select></label>
      <label>Language <select value={language} onChange={(e) => setLanguage(e.target.value)}>
        <option value="all">All</option>
        {languages.map(l => <option key={l}>{l}</option>)}
      </select></label>
      <p>{visible.length} repositories</p>
      <ul>
        {visible.map(r => (
          <li key={r.id}>
            <a href={r.html_url}>{r.name}</a> ⭐ {r.stargazers_count} {r.language && <em>{r.language}</em>}
            {r.description && <p>{r.description}</p>}
          </li>
        ))}
      </ul>
    </>
  );
}
```
```tsx
// src/App.tsx
import { useState } from "react";
import { SearchForm } from "./SearchForm";
import { RepoList } from "./RepoList";
import { useRepos } from "./useRepos";

export default function App() {
  const [user, setUser] = useState("");
  const state = useRepos(user);
  return (
    <main>
      <h1>Repo explorer</h1>
      <SearchForm onSearch={setUser} />
      {state.status === "loading" && <p role="status">Loading…</p>}
      {state.status === "error" && <p role="alert">{state.error}</p>}
      {state.status === "success" && <RepoList repos={state.repos} />}
    </main>
  );
}
```

> [!check] Checkpoint — search `facebook`, sort by name, filter by `TypeScript`. Search a nonsense name → see the error.

## Step 5 — Tests
```bash
npm i -D vitest jsdom @testing-library/react @testing-library/user-event @testing-library/jest-dom
```
`vite.config.ts` → `test: { environment: "jsdom", globals: true }`.

```tsx
// src/App.test.tsx
import "@testing-library/jest-dom/vitest";
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import App from "./App";

const repos = [
  { id: 1, name: "zeta", html_url: "#", description: null, stargazers_count: 5, language: "Go", updated_at: "2026-01-01" },
  { id: 2, name: "alpha", html_url: "#", description: "first", stargazers_count: 50, language: "TypeScript", updated_at: "2026-02-01" },
];

beforeEach(() => {
  vi.spyOn(globalThis, "fetch").mockResolvedValue(new Response(JSON.stringify(repos), { status: 200 }));
});
afterEach(() => vi.restoreAllMocks());

test("searches and sorts repos", async () => {
  const user = userEvent.setup();
  render(<App />);
  await user.type(screen.getByLabelText("GitHub user"), "ada");
  await user.click(screen.getByRole("button", { name: "Search" }));

  const items = await screen.findAllByRole("listitem");
  expect(items[0]).toHaveTextContent("alpha");                 // sorted by stars desc

  await user.selectOptions(screen.getByLabelText("Language"), "Go");
  expect(screen.getAllByRole("listitem")).toHaveLength(1);
});

test("shows error for unknown user", async () => {
  vi.mocked(fetch).mockResolvedValueOnce(new Response("{}", { status: 404 }));
  render(<App />);
  await userEvent.type(screen.getByLabelText("GitHub user"), "nobody-xyz");
  await userEvent.click(screen.getByRole("button", { name: "Search" }));
  expect(await screen.findByRole("alert")).toHaveTextContent('User "nobody-xyz" not found');
});
```

## 🏋️ Challenges
1. Debounce search-as-you-type with a `useDebounce` hook ([[React Custom Hooks]]).
2. Replace `useRepos` with TanStack Query ([[React Data Fetching]]).
3. Put `user`, `sort` and `language` in the URL query string so results are shareable.
4. Add pagination using GitHub's `Link` header ([[HTTP Headers]]).
5. Move state to Redux → [[Tutorial - React and Redux Toolkit Task Board]].

> [!success]- Solution sketch: challenge 3
> ```ts
> const params = new URLSearchParams(location.search);
> const [user, setUser] = useState(params.get("user") ?? "");
> useEffect(() => {
>   const p = new URLSearchParams(location.search);
>   user ? p.set("user", user) : p.delete("user");
>   history.replaceState(null, "", `?${p}`);
> }, [user]);
> ```

## Related
- [[Tutorials MOC]] · [[React useEffect]] · [[Jest Testing React Components]]
