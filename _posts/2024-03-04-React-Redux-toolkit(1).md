---
layout: post
title: React 71장 - Redux-Toolkit (1) redux 비교하기
author: admin
date: 2024-03-04 00:00:00 +900
lastmod: 2024-03-04  00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, redux, rtk, toolkit, state]
---

# React

```
npm i redux react-redux @reduxjs/toolkit
```

## Redux-Toolkit (RTK)

- Next.js에서 Redux-Toolkit을 사용하기 전에 자세히 알아보자
- 기존의 리덕스를 사용 시, 상태 관리를 할수록 코드가 많아지고 복잡해지는 문제점을 해결한 것이다.

### Redux vs Redux-Toolkit

#### Redux

- `reducer`를 구현할 때마다 `type`이름을 적어야하며, `switch/if`문을 반복적으로 사용해야 한다.

```jsx
// store.js
import { createStore } from 'redux';

const reducer = (state = { counter: 0 }, action) => {
  switch(action.type) {
    case 'increment':
      return { counter: state.counter + 1 }
    case 'decrement':
      return { counter: state.counter - 1 }
    default:
      return state
  }
}

const store = createStore(reducer);

export default store;

// App.jsx
import { useSelector, useDispatch } from 'react-redux'

function App() {
  const counter = useSelector((state) => state.counter);
  const dispatch = useDispatch();

  const increment = () => {
    dispatch({ type: 'increment' })
  }

  const decrement = () => {
    dispatch({ type: 'decrement' })
  }
  // ...
}

export default App
```

#### Redux-Toolkit

```jsx
// store/counterSlice.js
import { createSlice } from "@reduxjs/toolkit";

const initialState = {
  value: 0,
};

export const counterSlice = createSlice({
  name: "counter",
  initialState,
  reducers: {
    increment: (state) => {
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
  },
});

export const { increment, decrement } = counterSlice.actions;

export default counterSlice.reducer;

// store.js
import { configureStore } from "@reduxjs/toolkit";
import counterReducer from "./counterSlice";

export const store = configureStore({
  reducer: {
    counter: counterReducer,
  },
});

// App.jsx
import React from "react";
import { useSelector, useDispatch } from "react-redux";
import { decrement, increment } from "./store/counterSlice";

export function Counter() {
  const count = useSelector((state) => state.counter.value);
  const dispatch = useDispatch();

  return (
    <div>
      <div>
        <button
          aria-label="Increment value"
          onClick={() => dispatch(increment())}
        >
          Increment
        </button>
        <span>{count}</span>
        <button
          aria-label="Decrement value"
          onClick={() => dispatch(decrement())}
        >
          Decrement
        </button>
      </div>
    </div>
  );
}
```

###### Dispatch

- `dispatch`를 할 때마다 `action type / payload`를 작성해야 되던 것과 달리 **slice**에서 작성한 `reducer`을 바로 불러와서 사용이 가능하다.

```jsx
// redux
dispatch({type: Actions.Add, payload: {value: ...} })

// RTK
dispatch(add(...))
```

###### Store

- 기존 `createStore`가 아닌 `configureStore`를 사용한다.
- `redux-thunk`와 `devtools`를 기본으로 제공하며, `reducer`에서 반환된 새로운 상태를 `Store` 에서 관리한다.
- `createStore`와 비슷하지만 `{reducer: ...}`로 작성해야 한다.

```jsx
// redux
const store = createStore(reducer);

// RTK
const store = configureStore({
  reducer: reducer,
  middleWare:  (getDefaultMiddleware) => ...
})
```

###### Reducer

- 기존의 상태관리와는 달리 **mutating** 으로 작성하는 것이 가능하다.
- `immer`라이브러리를 사용하고 있기 때문에 실제로는 기존의 상태를 변형하는 것이 아니라, 기존 상태에 대한 변경을 감지하고 변경 상태를 바탕으로 새로운 **immutable state**를 생성한다.

```jsx
// redux
const reducer = (state, action) = {
  switch(action.type){
    case "increment" :
      return {...state, number: action.payload...}
  }
}

// RTK
const exampleReducerSlice = createReducer({
  name: "",
  initialState,
  reducers:{
    increment: (state, action) => {
      state.value = action.payload...
    }
  }
})
```
