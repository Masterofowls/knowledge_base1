---
tags: [redux, react, concept]
aliases: [connect, mapStateToProps, redux-thunk legacy, combineReducers]
created: 2026-10-26
status: growing
---
# Legacy Redux Patterns

You'll meet these in older codebases. Know how to read them and how to migrate.

```js
// constants.js
export const ADD_TODO = "ADD_TODO";
// actions.js
export const addTodo = (text) => ({ type: ADD_TODO, payload: { text } });
// reducer.js
export default function todos(state = [], action) {
  switch (action.type) {
    case ADD_TODO: return [...state, { text: action.payload.text, done: false }];
    default: return state;
  }
}
// store.js
import { createStore, combineReducers, applyMiddleware } from "redux";   // createStore is deprecated
import thunk from "redux-thunk";
const store = createStore(combineReducers({ todos }), applyMiddleware(thunk));

// Component with connect()
const mapStateToProps = (state) => ({ todos: state.todos });
const mapDispatchToProps = { addTodo };
export default connect(mapStateToProps, mapDispatchToProps)(TodoList);
```

## Migration path
1. Replace `createStore` with `configureStore` (accepts existing reducers).
2. Convert one reducer + its constants/actions to a `createSlice` at a time.
3. Replace `connect` with `useSelector`/`useDispatch` when touching a component.
4. Move data-fetching thunks to [[RTK Query]].
5. Delete action-type constants as they become unused.

## Related
- [[Redux Toolkit configureStore]] · [[Redux Toolkit createSlice]]
