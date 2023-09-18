---
layout: post
title: React 22장 - Redux 개념추가 (1)
author: admin
date: 2023-02-25 00:00:00 +900
lastmod: 2023-02-25 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT] # 대문자로 작성
tags: [react, components, jsx, state, useDispatch, useSelector]
---

# React

## Redux

- 자바스크립트 애플리케이션을 위한 라이브러리이다.
- 애플리케이션의 상태를 저장하고 업데이트하는 등의 관리를 할 때 사용한다.

### action

- Redux에서 상태 변화(`state`)를 일으키기 위해 발생하는 일종의 이벤트이다.
- `action`은 상태 변화를 나타내는 객체로, 반드시 `type`필드를 가지고 있어야 한다.
- `action`객체는 애플리케이션에서 발생하는 모든 이벤트를 표현하며, `type`은 `action`의 종류를 식별하는 문자열 값이다.
  - `action`의 종류를 나타내는 유일한 값이기 때문에 필수적이다.
- `action`은 보통 Action Creator(액션 생성자) 함수에 의해 생성된다.
  - Action Creator는 객체를 반환하며, 이 객체는 Redux Store에 전달되어 상태 변화를 일으키기 때문에 Redux에서 매우 중요한 역할을 한다.
- Redux의 기본 원칙 중 하나는 `state`를 직접적으로 수정하지 않는 것인데, 상태를 변경하기 위해서 `action`객체를 생성하고, 이 객체를 Reducer 함수에 전달하여 상태를 업데이트한다.
  - 이러한 과정을 거쳐, 애플리케이션의 상태를 예측할 수 있고 관리하기 쉬운 방식으로 업데이트하여 복잡도를 줄일 수 있다.

### payload

- `action`객체의 속성 중 하나로, 객체가 전달하는 데이터를 담고 있다.
- 보통 `type`과 `payload`라는 두 개의 속성을 갖고 있다.
- `type` 에 따라 데이터의 종류와 형식이 달라질 수 있는데, 데이터를 담는 형식은 보통 객체이지만, 문자열, 숫자, 배열 등의 다양한 형식으로 표현할 수 있다.

```jsx
const loginAction = {
  type: "LOGIN",
  payload: {
    username: "user1",
    authToken: "xxxxxx",
  },
};
```

### reducer

- 애플리케이션의 상태를 단일 객체인 Redux Store에 저장하는데, Redux Store의 상태는 액션(`action`)을 통해서만 변경할 수 있다.
- `action`은 일반 객체이며, `type`을 갖고있다.
- `action`은 Redux Store에 전달되어, Store가 각각의 Reducer 함수를 호출하고 `action`타입에 따라 새로운 상태를 생성한다.
- `reducer`함수는 이전 상태와 `action`을 인자로 받아 새로운 상태를 반환하며, 이전 상태는 변경되지 않고 새로운 상태가 반환되기 때문에 불변성이 유지된다.
- `reducer`함수는 순수 함수여야 하며, 이전 상태와 `action`객체만을 사용하여 새로운 상태를 생성한다.
- Redux Store는 이전 상태, 새로운 상태 및 현재 `action`에 대한 정보를 유지한다.
- 이 정보는 Redux DevTools와 같은 도구를 사용하여 디버그하고 추적할 수 있다.
- Reducer는 Redux의 핵심 개념 중 하나이며, Redux의 불변성과 예측 가능성을 보장하는데 매우 중요한 역할을 한다.

#### switch

- Reducer 함수에서 주로 사용되며 `action`객체의 `type`을 검사하는 역할을 한다.
- `switch`문은 각각의 `case`에 해당하는 `action`타입을 정하고, 해당 `case`에서 상태 객체를 새롭게 업데이트 하는 로직을 작성한다.
- Reducer 함수는 이전 상태와 `action`객체를 인자로 받고, `switch`문에서는 객체의 `type` 조건을 사용한다.
- 각 `case`에서 상태 객체를 새로운 값으로 업데이트하고, 새로운 상태 객체를 반환한다.
- `default case`에서는 이전 상태를 그대로 반환한다.

```jsx
const initialState = { count: 0 };

function counterReducer(state = initialState, action) {
  switch (action.type) {
    case "PLUS":
      return { count: state.count + 1 };

    case "MINUS":
      return { count: state.count - 1 };

      defualt: return state;
  }
}
```

### useSelector

- React 바인딩 라이브러리인 react-redux에서 제공하는 함수 중 하나이다.
- Redux store에서 `state`값을 가져오는 데 사용된다.
- Redux store에 상태값이 바뀐 경우, 바뀐 store의 상태값을 다시 가져와 컴포넌트를 렌더링 시킨다.

```jsx
import { useSelector } from "react-redux";

const fruitList = useSelector((state) => state.fruit);
```

```jsx
const SET_FRUIT_LIST = "fruit/SET_FRUIT_LIST";

export const setFruitList = (fruitList) => ({
  type: SET_FRUIT_LIST,
  fruitList,
});

const initialState = {
  name: false,
  price: false,
};

export default function fruit(state = initialState, action) {
  switch (action.type) {
    case "SET_FRUIT_LIST":
      return {
        ...state,
        name: action.fruitList,
      };

    default:
      return state;
  }
}
```

```jsx
const fruit = useSelector((state) => state.fruit);
dispatch(setFruitList("딸기"));
console.log(fruit.name); // "딸기"
console.log(fruit.price); // false
```

### useDispatch

- React 바인딩 라이브러리인 react-redux에서 제공하는 함수 중 하나이다.
- Redux store에서 작성한 `action`을 불러오는 데 사용한다.
- `useDispatch`함수는 `dispatch`함수를 사용하여 Redux 상태를 업데이트한다.
- 생성한 `action`을 `dispatch`를 사용하여 발생시킬 수 있다.

```jsx
import { useDispatch } from "react-redux";

function MyComponent() {
  const dispatch = useDispatch();

  const handleClick = () => {
    dispatch({ type: "INCREMENT" });
  };

  return (
    <div>
      <button onClick={handleClick}>increment</button>
    </div>
  );
}
```
