---
tags: [react, guide]
aliases: [Learn React, React roadmap]
created: 2026-10-26
---
# React Getting Started

## 1. Create a project
```bash
npm create vite@latest my-app -- --template react-ts
cd my-app && npm i && npm run dev       # http://localhost:5173
```
Full-stack framework instead? `npx create-next-app@latest` (Next.js) or React Router's framework mode.
Install the **React Developer Tools** browser extension.

## 2. Hello component
```tsx
import { useState } from "react";

export default function App() {
  const [count, setCount] = useState(0);
  return (
    <main>
      <h1>Hello React</h1>
      <button onClick={() => setCount(c => c + 1)}>Clicked {count} times</button>
    </main>
  );
}
```

## 3. Learning checklist
- [ ] JSX & components → [[React Components and JSX]]
- [ ] Props, state, lifting state up → [[React State and Props]]
- [ ] Lists & keys → [[React Lists and Keys]]
- [ ] Forms → [[React Forms]]
- [ ] Effects (and when not to use them) → [[React useEffect]]
- [ ] Refs & memoization → [[React useRef useMemo useCallback]]
- [ ] Context → [[React Context]]
- [ ] Custom hooks → [[React Custom Hooks]]
- [ ] Rendering model → [[React Rendering and Reconciliation]]
- [ ] Data fetching → [[React Data Fetching]]
- [ ] TypeScript with React → [[React with TypeScript]]
- [ ] Practise: [[Tutorial - React App with Vite and TypeScript]]

## 4. Beginner mistakes
- Mutating state (`state.items.push(x)`) — React won't re-render.
- Using `useEffect` to derive data or sync state that could be computed.
- Missing or index-based `key`s.
- Defining components inside components.
- Reading state right after `setState` and expecting the new value.

## 5. Next steps
[[Redux Getting Started]] · [[Jest Getting Started]] (React Testing Library) · [[Playwright Getting Started]].

## Resources
- react.dev — the official docs with interactive tutorials (start with "Thinking in React")
- TanStack Query docs — server state
- Testing Library docs — testing-library.com
