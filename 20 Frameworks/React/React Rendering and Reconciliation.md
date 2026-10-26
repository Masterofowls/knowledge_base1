---
tags: [react, concept]
aliases: [re-render, reconciliation, virtual DOM, React.memo]
created: 2026-10-26
status: evergreen
---
# React Rendering and Reconciliation

> [!summary] TL;DR
> **Render** = calling your component to get JSX. **Commit** = applying the diff to the DOM. A component re-renders when **its state changes, its parent re-renders, or a context it uses changes**. Props changing is *not* the trigger — parent rendering is.

## Pipeline
1. **Trigger** — `setState`, parent render, context change.
2. **Render** — call components, build new element tree.
3. **Reconcile** — diff against previous tree (same type + same key → update; else remount).
4. **Commit** — mutate DOM, run layout effects, then paint, then `useEffect`s.

## Diffing heuristics
- Different element type at the same position → **unmount whole subtree** (state lost).
- Lists matched by `key` → [[React Lists and Keys]].

> [!warning] Defining a component inside another component creates a new type every render → remount + lost state each time.

## Avoiding wasted renders
- Move state down (closer to where it's used).
- Pass components as `children` (they don't re-render with the parent's state).
- `React.memo(Component)` + stable props ([[React useRef useMemo useCallback]]).
- Profile first: React DevTools Profiler, "highlight updates".

## Related
- [[React State and Props]]
- [[React Context]]
