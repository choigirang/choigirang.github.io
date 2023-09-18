---
layout: post
title: React 20장 - Redux
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

- Redux 개요

![Redux 개요](https://user-images.githubusercontent.com/118104644/221072520-835af78a-2d36-4ba4-9472-c045d75e6e9f.png){: .align-center width="600"}

## Props drilling

- props dilling 의 예시
- 아래와 같은 구조의 React가 있을 때 해당 배치 상태는 다소 비효율적이다.
- 상태 끌어올리기와 Props 내려주기를 여러 번 거쳐야하며 애플리케이션이 복잡해질수록 데이터 흐름이 복잡해진다.
- 컴포넌트 구조가 바뀐다면 데이터의 흐름을 바꿔야 할 수도 있다.

![react_redux_props_drilling](https://user-images.githubusercontent.com/118104644/221072790-1ba1358a-7de8-4d09-95e6-12b14edc6ff9.png){: .align-center width="600"}
![react_redux_props_drilling2](https://user-images.githubusercontent.com/118104644/221072792-03dc864a-0ed3-48a5-9d84-276ddd48f5c8.gif){: .align-center width="600"}

## Redux를 사용할 때의 데이터 흐름과 구조

- Redux는 전역 상태를 관리할 수 있는 저장소인 Store를 제공한다.
- 상태가 변경되어야 하는 이벤트가 발생하면 변경될 상태에 대한 정보가 담긴 **Action 객체**가 생성된다.
- Action 객체는 **Dispath 함수**의 인자로 전달된다.
- Dispatch 함수는 Action 객체를 **Reducer 함수**로 전달해준다.
- Reducer 함수는 Action 객체의 값을 확인하고, 그 값에 따라 **전역 상태 저장소 Store**의 상태를 변경한다.
- 상태가 변경되면 React는 화면을 다시 렌더링 한다.

- Redux 에서는 **Action=>Dispatch=>Reducer=>Store** 순으로 데이터가 단방향으로 흐르게 된다.

![react_redux](https://user-images.githubusercontent.com/118104644/221072794-8247cfb7-e5ae-45c6-a068-801953fb9a3a.gif){: .align-center width="600"}
![react_redux2](https://user-images.githubusercontent.com/118104644/221072795-a578e019-b989-4de0-b71a-9b4d7ac58490.gif){: .align-center width="600"}

## 예시

### App.js

```jsx
import React from "react";

export default function App() {
  return (
    <div>
      <h1>{`Count: ${1}`}</h1>
    </div>
  );
}
```

### index.js

```jsx
import React from "react";
import { createRoot } from "react-dom/client";
import App from "./App";

const rootElement = document.getElementById("root");
const root = creatRoot(rootElement);

root.render(<App />);
```

### Store

- 상태가 관리되는 하나뿐인 저장소의 역할을 한다.
- Redux앱의 state가 저장되어 있는 공간이다.
- `createStore` 메서드를 활용해 Reducer를 연결해서 Store를 생성할 수 있다.

```jsx
import { create } from "redux";
const store = createStore(rootReducer);
```

- `react-redux`에서 `Provider`를 불러온다. (`import`)

```jsx
import { Provider } from "react-redux";
```

- Provider는 store(저장소)를 손 쉽게 사용할 수 있는 컴포넌트이다.
- `legacy_createStore`를 redux에서 불러온다.

```jsx
import { legacy_createStore as createStore } from "redux";
```

- 해당 컴포넌트를 불러온 다음 store를 사용할 컴포넌트로 감싸준 후 Provider 컴포넌트의 `props`로 store를 설정해주면 된다.

```jsx
root.render(
  <Provider store={store}>
    <App />
  </Provider>
);
```

- 변수 `store`에 `createStore`메서드를 할당해준다.
  - `createStore`의 인자로 `counterReducer`함수를 전달해준다.

```jsx
const store = createStore(counterReducer);
```

### Reducer

- `Reducer`는 외부 요인으로 인해 엉뚱한 값으로 상태가 변경되지 않도록 순수함수여야 한다.
- 우리가 `store`에 할당한 전달 인자 함수를 작성한다.
  - Reducer 함수는 Dispatch에게서 전달받은 Action 객체의 `type`값에 따라 상태를 변경시키는 함수이다.
  - 첫 번째 인자에는 기존 `state`가 들어오게 하고, `default value`를 설정하지 않으면 `undefined`가 할당되기 때문에 꼭 설정해주어야 한다.
  - 두 번째 인자에는 `action`객체가 들어오게 한다.
  - `action`객체에서 정의한 `type`에 따라 새로운 `state`를 리턴한다.
  - 새로운 `state`가 Store에 저장된다.
  - `action`이 `INCREASE`이거나 `DECREASE`,`SET_NUMBER`인 경우가 아니면 기존 상태 그대로를 리턴한다.

```jsx
const counterReducer = (state = count, action) => {
  switch (action.type) {
    case "INCREASE":
      return state + 1;

    case "DECREASE":
      return state - 1;

    case "SET_NUMBER":
      return action.payload;

    default:
      return state;
  }
};
```

### Action

- 우리가 함수 안에 정의한 `case action`으로 어떠한 동작을 할 것인지 설정해준다.
- `type`은 꼭 지정해 주어야 하며, 해당 동작을 명시해주는 역할을 하기 때문에 **대문자와 Snake Case**로 작성해야 한다.
- 필요에 따라 `payload`를 작성해 구체적인 값을 전달한다.

  ```jsx
  {type: "INCREASE"}

  {type: "SET_NUMBER", payload: 5}
  ```

- `action`은 보통 함수 형태로 만들어 사용하며 이러한 함수를 **액션 생성자**라고 부른다.

  - 다른 파일에서도 사용하기 위해 `export`해준다.

    ```jsx
    const increase = () => {
      return {
        type: "INCREASE",
      };
    };

    const setNumber = (num) => {
      return {
        type: "SET_NUMBER",
        payload: num,
      };
    };
    ```

- `type`작성해보기

  ```jsx
  export const increase = () => {
      return (
          type: "INCREASE",
      )
  }

  export const decrease = () => {
      return (
          type: "DECREASE",
      )
  }
  ```

### Dispatch

- `Reducer`로 Action을 전달해주는 함수이다.
- Dispatch의 전달인자로 Action의 객체가 전달된다.

  ```jsx
  // Action 객체를 직접 작성하는 경우
  dispatch({ type: "INCREASE" });
  dispatch({ type: "SET_NUMBER", payload: 5 });

  // 액션 생성자(Action Creator)를 사용하는 경우
  dispatch(increase());
  dispatch(setNumber(5));
  ```

### Redux Hooks

- Store, Reducer, Action, Dsipatch 를 구성하는 것을 완료했으면, 이 개념들을 연결시켜주어야 한다.
- 이때, Redux Hooks를 사용한다.
- Redux Hooks는 Redux를 사용할 때 활용할 수 있는 Hooks 메서드를 제공한다.
- 그 중에서 크게 `useSelector()`,`useDispatch()`를 기억하면 된다.

#### useDispatch()

- `useDispatch()`는 Action 객체를 Reducer로 전달해 주는 Dispatch 함수를 반환하는 메서드이다.

  ```jsx
  import { useDispatch } from "react-redux";

  const dispatch = useDispatch();
  dispatch(increase());
  console.log(counter); // 2

  dispatch(setNumber(5));
  console.log(counter); // 5
  ```

### useSelector()

- `useSelector()`는 컴포넌트와 `state`를 연결하여 Redux의 `state`에 접근할 수 있게 해주는 메서드이다.

  ```jsx
  import { useSelector } from "react-redux";
  const counter = useSelector((state) => state);
  console.log(counter); // 1
  ```

- App.js 에 `useDispatch`를 불러온다.
- index,js에서 `increase`와 `decrease` 함수를 불러온다.

  ```jsx
  import { useDispatch } from "react-redux";
  import { increase, decrease } from "./index.js";
  ```

- `useDispatch`의 실행 값을 변수에 저장하여 `dispatch`함수를 사용한다.
  ```jsx
  const dispatch = useDispatch();
  ```
- 이벤트 핸들러 안에서 `dispatch`를 통해 action 객체를 Reducer 함수로 전달해준다.

## 코드

### App.js

```jsx
import React from "react";
import { useDispatch } from "react-redux";
import { increase, decrease } from "./index.js";

export default function App() {
  const dispatch = useDispatch();
  const state = useSelector((state) => state);

  const plusNum = () => {
    dispatch(increase());
  };

  const minusNum = () => {
    dispatch(decrease());
  };

  return (
    <div className>
      <h1>{`Count: ${state}`}</h1>
      <div>
        <button onClick={plusNum}>+</button>
        <button onClick={minussNum}>-</button>
      </div>
    </div>
  );
}
```

### index.js

```jsx
import React from "react"
import {createRoot} from "react-dom/client"
import App from "./App.js"
import {Provider} from "react-redux"
import {legacy_createStore as createStore} from "redux"

const rootElement = document.getElementById("root")
const root = createRoot(rootElement)

export const increase = () => {
    return {
        type: "INCREASE"
    }
}

export const decrease = () => {
    return {
        type: "DECREASE"
    }
}

const count = 1;

const counterReducer = (state=count, action) => {
    switch (action.type){
        case "INCREASE":
            return state + 1

        case "DECREASE":
            return state - 1

        case "SET_NUMBER":
            return action.payload

        default:
            return state
    }
}

const store = createStore(counterReducer)

root.render(
    <Provider store={store}>
        <App />
    </Provider>
)asdasd
```

[StackBlitz 작성해보기](https://stackblitz.com/edit/react-jq4f2e?file=src%2FREADME.md){: target="\_blank"}
