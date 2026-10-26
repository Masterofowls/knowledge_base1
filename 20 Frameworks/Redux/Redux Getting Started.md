---
tags: [redux, guide]
aliases: [Learn Redux, Redux roadmap]
created: 2026-10-26
---
# Redux Getting Started

> [!question] Do you need Redux?
> Read [[Redux vs Context vs Zustand]] first. Learn React state well before adding Redux.

## 1. Install
```bash
npm i @reduxjs/toolkit react-redux
# or start from the official template:
npx degit reduxjs/redux-templates/packages/vite-template-redux my-app
```
Install the **Redux DevTools** browser extension.

## 2. Minimal working example
```ts
// counterSlice.ts
import { createSlice } from "@reduxjs/toolkit";
const counter = createSlice({
  name: "counter",
  initialState: { value: 0 },
  reducers: { incremented: (s) => { s.value += 1; } },
});
export const { incremented } = counter.actions;
export default counter.reducer;

// store.ts
import { configureStore } from "@reduxjs/toolkit";
import counterReducer from "./counterSlice";
export const store = configureStore({ reducer: { counter: counterReducer } });
export type RootState = ReturnType<typeof store.getState>;
```
```tsx
// main.tsx: <Provider store={store}><App /></Provider>
// App.tsx
const value = useSelector((s: RootState) => s.counter.value);
const dispatch = useDispatch();
return <button onClick={() => dispatch(incremented())}>{value}</button>;
```

## 3. Learning checklist
- [ ] Store / action / reducer data flow → [[Redux Core Concepts]]
- [ ] `configureStore` + typed hooks → [[Redux Toolkit configureStore]]
- [ ] `createSlice` & Immer → [[Redux Toolkit createSlice]]
- [ ] `useSelector` / `useDispatch` → [[React Redux Hooks]]
- [ ] Memoized selectors → [[Redux Selectors and Reselect]]
- [ ] Async thunks → [[Redux Async Thunks]]
- [ ] RTK Query → [[RTK Query]]
- [ ] Normalized state → [[Redux Normalized State]]
- [ ] Middleware & DevTools → [[Redux Middleware and DevTools]]
- [ ] Testing → [[Redux Testing]]
- [ ] Reading old code → [[Legacy Redux Patterns]]
- [ ] Practise: [[Tutorial - React and Redux Toolkit Task Board]]

## 4. Beginner mistakes
- Putting everything (form inputs, modal open flags) in Redux.
- Selectors that return new arrays/objects each call → re-render storms.
- Non-serializable values (Dates, Promises, class instances) in state.
- Writing thunks + loading flags for server data instead of RTK Query.
- Both mutating `state` *and* returning a value from a reducer.

## Resources
- redux.js.org — "Redux Essentials" tutorial (RTK-first)
- redux-toolkit.js.org — API reference
- RTK Query docs — redux-toolkit.js.org/rtk-query/overview
