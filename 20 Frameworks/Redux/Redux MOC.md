---
tags: [moc, redux, react]
aliases: [Redux, React Redux, Redux Toolkit, RTK]
created: 2026-10-26
---
# Redux MOC

> [!abstract] Scope
> Redux with **Redux Toolkit (RTK)** and **React-Redux** hooks — the modern, official way. Legacy hand-written action types, `switch` reducers and `connect()` are covered only for reading old code.

- 🚀 [[Redux Getting Started]] — install, first store, learning checklist
- 🛠️ Tutorial: [[Tutorial - React and Redux Toolkit Task Board]]

## Core ideas
- [[Redux Core Concepts]] — store, actions, reducers, one-way data flow
- [[Redux Toolkit configureStore]] — store setup, typed hooks
- [[Redux Toolkit createSlice]] — reducers + actions with Immer

## Using it in React
- [[React Redux Hooks]] — `useSelector`, `useDispatch`, `Provider`
- [[Redux Selectors and Reselect]] — derived data, memoization

## Async & server data
- [[Redux Async Thunks]] — `createAsyncThunk`
- [[RTK Query]] — data fetching & caching
- [[Redux Normalized State]] — `createEntityAdapter`

## Advanced
- [[Redux Middleware and DevTools]] — listener middleware, logging, time travel
- [[Redux Testing]]
- [[Redux vs Context vs Zustand]] — when to use what
- [[Legacy Redux Patterns]] — `connect`, switch reducers, action constants

## Cross-links
- [[React MOC]] · [[React Context]] · [[React Data Fetching]] · [[React with TypeScript]]
- [[JS Destructuring and Spread]] (immutable updates) · [[TS Unions and Narrowing]]
