---
layout: post
title: React 21장 - Cmarket Redux (1)
author: admin
date: 2023-02-24 00:00:00 +900
lastmod: 2023-02-24 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT] # 대문자로 작성
tags: [react, components, jsx, state, ui, props, drilling]
---

# React

## 자료구조

- src
  - actions
    - index.js
  - components
    - CartItem.js
    - Item.js
    - Nav.js
    - NotificationCenter.js
    - OrderSummary.js
    - Toast.js
  - pages
    - ItenListContainer.js
    - Shopping.js
  - reducers
    - index.js
    - inoitialState.js
    - itemReducer.js
    - notificationReducer.js
  - store
    - store.js
  - App.js
  - index.js

## store/store.js

- `createStore`메서드를 사용하여 `reducers/index.js`에서 받아온 `rootReducer`와 `composeEnhancers` 함수를 넘겨준다.
  - Redux DevTools Extension은 Reudx의 상태 변화를 모니터링하고 디버깅하기 위한 도구이다.
  - Redux store의 상태를 시각화하고, 상태 변경의 히스토리를 확인하며 `action`을 `dispatch`하면서 상태 변화를 확인할 수 있도록 돕는다.
  - `composeEnhancers`는 Redux DevTools Extension을 사용할 수 있는 경우, Redux store를 구성하기 위한 함수이다.
  - `compose`함수는 Redux store 에 미들웨어를 적용하기 위해 사용되는 함수이다.
  - `composeEnhancers`함수는 `compose`함수를 확장하여 Redux DevTools Extenstion을 사용할 수 있도록 한다.
    - Redux는 애플리케이션의 상태를 하나의 `store`에 저장하고 이 상태를 변경하는, `action`을 `dispatch`하면서 상태를 업데이트하는데, 상태를 처리하는 함수를 `reducer`라고 한다.
    - 애플리케이션의 규모가 커지면서 `reducer`의 갯수가 늘어나는데 이러한 경우, `rootReducer`함수를 사용하여 각각의 `reducer`를 하나로 합칠 수 있다.
    - 합쳐진 `reducer`는 Redux store의 상태를 변경한다.
- `rootReducer`는 Redux store를 생성할 때 첫 번째 인수로 전달된다
  - 애플리케이션의 모든 상태를 담는 객체를 반환하는데, 이 객체의 각 속성은 각 `reducer`에서 처리하는 상태의 일부를 나타내며, Redux store의 상태가 변경될 때마다 해당 속성의 값이 업데이트 된다.

## reducer/index.js

- `combineReducers`함수를 사용하여 여러 개의 `reducer`를 합친다.
  - `rootReducer`은 Redux에서 사용되는 여러 개의 reducer를 하나로 합치기 위한 함수이다.
  - 객체 형태로 전달되는 `reducer`들을 하나로 합치고, 각 처리되는 액션 타입이 맞는 경우 해당 `reducer`의 상태를 업대이트한다.
- initialState.js 와 notificationReducer.js로부터 `reudcer`을 받아 하나의 `reducer`로 `combine`한다.

```jsx
import { combineReducers } from "redux";
import itemReducer from "./itemReducer.js";
import notificationReducer from "./notificationReducer.js";

const rootReducer = combineReducers({
  itemReducer,
  notificationReducer,
});

export default rootReducer;
```

## actions/index.js

- Redux를 사용하기 위해 `Action`을 부여한다.
  - Redux에서는 `state`를 변경시키기 위해 `setState`가 아닌 `action`을 사용해야 한다.
- `payload`에 담긴 구체적인 값을 사용한다.
- `action`의 `type`에 따라 `payload`는 어떠한 형태의 값을 갖고 있다.

```jsx
export const ADD_TO_CART = "ADD_TO_CART";
export const REMOVE_FROM_CART = "REMOVE_FROM_CART";
export const SET_QUANTITY = "SET_QUANTITY";
export const NOTIFY = "NOTIFY";
export const ENQUEUE_NOTIFICATION = "ENQUEUE_NOTIFICATION";
export const DEQUEUE_NOTIFICATION = "DEQUEUE_NOTIFICATION";

export const addToCart = (itemId) => {
  return {
    type: ADD_TO_CART,
    payload: {
      quantity: 1,
      itemId,
    },
  };
};

export const removeFromCart = (itemId) => {
  return {
    type: REMOVE_FROM_CART,
    payload: {
      itemId,
    },
  };
};

export const setQuantity = (itemId, quantity) => {
  return {
    type: SET_QUANTITY,
    payload: {
      itemId,
      quantity,
    },
  };
};

export const notify =
  (message, dismissTime = 5000) =>
  (dispatch) => {
    const uuid = Math.random();
    dispatch(enqueueNotification(message, dismissTime, uuid));
    setTimeout(() => {
      dispatch(dequeueNotification());
    }, dismissTime);
  };

export const enqueueNotification = (message, dismissTime, uuid) => {
  return {
    type: ENQUEUE_NOTIFICATION,
    payload: {
      message,
      dismissTime,
      uuid,
    },
  };
};
```

## reducer/itemReducer.js

- actions/index.js로부터 `action`에 관한 `type`을 받아 해당 `action`이 실행됐을 때, Redux Store에 저장되어 상태 변화를 일으킬 객체를 설정한다.
- 주로 `switch`문을 사용하여 객체의 `type`을 검사한다.
  - 각각의 `case`에 해당하는 타입을 지정하고, 해당 `case`에 따른 상태 객체를 새로운 값으로 업데이트한다.
  - 각 `case`에서는 상태 객체를 새로운 값으로 업데이트하고 새로운 상태 객체를 반환한다.
  - `default case`에서는 이전 상태를 그대로 반환한다.
- action/index.js 에서 작성한 `action`의 타입을 받아온다.
- `ADD_TO_CART`라는 `action`이 발생하면 `state`(initialState)의 `cartItems`는 기존의 값에 우리가 클릭한 요소의 `id`와 `quantity`를 담고있는 `payload`를 추가한다.
  - actions/index.js에서 작성한 `addToCart` 함수는 `itemId`를 인자로 받는다.
- `REMOVE_FROM_CART`라는 `action`이 발생하면 `state`의 `cartItems`는, `removeFromCart`함수가 실행됐을 때에 발생하는 `action`이다.
  - 함수는 `itemId`라는 인자를 받아와 `payload`에 `itemId`를 담고 있는데, 우리가 넘겨받은 인자의 `id`가 기존의 `initialState`에 있는지 비교하고, `cartItems`에 우리가 클릭한 `id`가 있을 경우, 이를 `filter`를 사용하여 제외시킨 새로운 `cartItems` 배열을 생성한다.
- `SET_QUANTITY`라는 `action`이 발생하면 `state`의 `cartItems`는, `setQuantity`함수가 실행됐을 때에 발생하는 `action`이다.
  - 함수는 `itemId`와 `quantity`를 인자로 넘겨받는데, 클릭한 상품의 `qauntity`와 `id`를 `payload`로 갖고 있다.
  - `idx`는 장바구니에 담긴 상품 중 우리가 클릭한 상품의 `id`(action.payload.itemId)와 일치하는 상품의 `id`를 찾아준다.
  - `idx`는 새로운 객체를 생성하는데에 사용되는데, 기존의 장바구니의 상품 중 우리가 클릭한 `idx`의 앞까지 복사하고, 우리가 선택한 상품의 `id`와 `quantity`를 담고 있는 `payload`를 그대로 넣어준다.
  - 그리고 나머지 상품들을 복사하여 새로운 객체를 생성한다.
- `Object.assign`을 사용하는 이유는, 기존의 객체를 그대로 사용하지 않아 불변성을 지키기 위해서이다.

```jsx
import { REMOVE_FROM_CART, ADD_TO_CART, SET_QUANTITY } from "../actions/index";
import { initialState } from "./initialState.js";

const itemReducer = (state = initialState, action) => {
  switch (action.type) {
    case ADD_TO_CART:
      return Object.assign({}, state, {
        cartItems: [...state.cartItems, action.payload],
      });

    case REMOVER_FROM_CART:
      return Object.assign({}, state, {
        cartItems: state.cartItems.filter(
          (el) => el.itemdId !== action.payload.itemId
        ),
      });

    case SET_QUANTITY:
      let idx = state.cartItems.findIndex(
        (el) => el.itemId === action.payload.itemId
      );

      return Object.assign({}, state, {
        cartItems: [
          ...state.cartItems.slice(0, idx),
          action.payload,
          ...state.cartItems.slice(idx + 1),
        ],
      });

    default:
      return state;
  }
};

export default itemReducer;
```

## App.js

- App 컴포넌트는 Nav, ItemListContainer, NotificationContainer, ShoppingCart 컴포넌트를 불러와 사용한다.
- `BrowserRouter`를 이용하여 선택하는 컴포넌트마다 다른 주소를 연결한다.
  - `Route`를 이용하여 각 컴포넌트에 주소값을 부여한다.

```jsx
import React, { useState } from "react";
import Nav from "./components/Nav";
import ItemListContainer from "./pages/ItemListContainer";
import NotificationCenter from "./components/NotificationCenter";
import { BrowserRouter as Router, Routes, Route } from "react-router-dom";
import ShoppingCart from "./pages/ShoppingCart";

function App() {
  return (
    <Router>
      <Nav />
      <Routes>
        <Route path="/" element={<ItemListContainer />} />
        <Route path="/shoppingcart" element={<ShoppingCart />} />
      </Routes>
      <NotificationCenter />
      <img
        id="logo_foot"
        src={`${process.env.PUBLIC_URL}/codestates-logo.png`}
        alt="logo_foot"
      />
    </Router>
  );
}

export default App;
```
