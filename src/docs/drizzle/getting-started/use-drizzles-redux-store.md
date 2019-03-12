---
title: Drizzle | Use Drizzle's Redux Store
layout: docs.hbs
---
# Use Drizzle's Redux Store

For those times when you don't want to manage your own Redux store, drizzle has you covered. Simply pass your sagas, reducers and drizzleOptions to
`generateStore` and drizzle will incorporate them in its own Redux Store.


These two snippets show how a saga to retrieve Todos from a web service endpoint by delegating to drizzle.
```javascript
// ./state.js

import { put, takeEvery } from 'redux-saga/effects'
import { generateStore } from 'drizzle'
import drizzleOptions from './drizzleOptions'

// actions
const TODOS_FETCH = 'MY_APP/TODOS_FETCH'
const TODOS_RECEIVED = 'MY_APP/TODOS_RECEIVED'

const todosReducer = (state = [], action) => {
  if (action.type === TODOS_RECEIVED) {
    // update your state
    return action.todos
  }
  return state
}

// fetch data from service
function *fetchTodos() {
  const todos = yield fetch('https://jsonplaceholder.typicode.com/todos')
                      .then(resp => response.json())
                      .then(json => json)
  yield put({ type: TODOS_RECEIVED, todos })
}

// app root saga
function *appRootSaga() {
  yield takeEvery(TODOS_FETCH, fetchTodos)
}

// app Reducers and Sagas
const appReducers = { todos: todosReducer }
const appSagas = [appRootSaga]

const store = generateStore({
  drizzleOptions,
  appReducers,
  appSagas
})

export default store

```

Finally, use the store as your normally would. In this example, we're using the `<DrizzleProvider>` from `drizzle-react`.

```javascript
// ...
import store from './store'
// ...

ReactDOM.render((
    <DrizzleProvider options={drizzleOptions} store={store}>
      <LoadingContainer>
        <Router history={history}>
          <Route path="/" component={App}>
            <IndexRoute component={HomeContainer} />
          </Route>
        </Router>
      </LoadingContainer>
    </DrizzleProvider>
  ),
  document.getElementById('root')
);
```
