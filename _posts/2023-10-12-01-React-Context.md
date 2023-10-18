---
layout: post
title: React 59장 - React Context (1)
author: admin
date: 2023-10-18 00:00:00 +900
lastmod: 2023-10-18  00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, query, context, redux, recoil, state]
---

# React

## Context API

- [공식문서](https://ko.legacy.reactjs.org/docs/context.html)
- `props drilling`을 피하고 상태를 전달하기 위해서 사용한다.
- 문서에 나와있듯이 꼭 `context` 를 사용하기보다 컴포넌트 합성을 통해 해결하는 것이 더 좋은 방법일 수 있다.
- `Context API`를 사용하면 컴포넌트 재사용성이 떨어지기에 주의해야 한다는 설명도 있다
- `props`로 전달할 경우, `props`에 의해서 항상 정해진 값이 나오는 컴포넌트가 되지만, `Context`를 사용할 시, 외부의 값에 의존하게 되기 때문에 다른 곳에서 사용하기 어려운 컴포넌트가 될 수 있다.

### createContext

```jsx
const initialValue = "default";

const Example = React.createContext(initialValue);
```

- `Context` 객체를 만들고 컴포넌트가 렌더링 될 때 `Provider`를 사용하여 값을 읽는다.

### Provider

```jsx
<Parent.Provider value={Example}></Parent.Provider>
```

- `Provider`가 감싸고 있는 하위 컴포넌트에서 `Context`에 저장된 값에 접근할 수 있다.

### Consumer

```jsx
import Example from "Example";

return (
  <div>
    <Example.Consumer>{(value) => <div>{value}</div>}</Example.Consumer>
  </div>
);
```

- `Context`에서 제공하는 값을 사용하기 위해 사용한다.

### useContext

- 리액트 버전에 따라 공식문서에서조차 사용을 지양하던 훅이지만, 많이 변경되어 이제는 대중적으로 사용하는 훅이다.
- `Consumer`보다 간단하게 상태에 접근할 수 있다.

```jsx
const value = useContext(Example);

return <div>{value}</div>;
```

## 정리

- `Provider`에서 제공한 `value`가 달라지면, `useContext`를 사용하고 있는 모든 컴포넌트에서 리렌더링이 발생한다.
  - `Context`는 여러 개 사용할 수 있기 때문에, 별도의 `Context`로 사용하거나 `useMemo`를 사용하여 방지한다.

```jsx
export const UserContext = createContext({
  setLoggedIn: () => {},
  setLoading: () => {},
});
const GrandParent = () => {
  const [loggedIn, setLoggedIn] = useState(false);
  const [loading, setLoading] = useState(false);
  const value = useMemo(
    () => ({ setLoggedIn, setLoading }),
    [setLoggedIn, setLoading]
  );
  return (
    <UserContext.Provider value={value}>
      <Parent />
      <div>{loggedIn ? "로그인" : "로그인안해"}</div>
      <div>{loading ? "로딩중" : "로딩안해"}</div>
    </UserContext.Provider>
  );
};

const Children = () => {
  const { setLoading, setLoggedIn } = useContext(UserContext);
  return (
    <>
      <button onClick={() => setLoading((prev) => !prev)}>로딩토글</button>
      <button onClick={() => setLoggedIn((prev) => !prev)}>로딩토글</button>
    </>
  );
};
```

## with TypeScript

- 타입스크립트를 사용하면 `createContext`의 초깃값인 `defaultValue`를 지정해주어야 한다.
- 전역적으로 사용할 상태값의 형태와 `defaultValue`의 형태를 맞추어주어야 한다.
- 예를 들어 `useState`로 상태값을 변경하는 `set`함수와 변경된 값을 전역적으로 사용한다고 했을 때, 형태는 아래와 같다.

```tsx
type DefaultValue = {
  changeStr: string;
  setChangeStr: React.Dispatch<React.SetStateAction<string>>;
};
const AppContext = createContext({
  changeStr: "",
  setChangeStr: () => {},
});

function App() {
  const [changeStr, setChangeStr] = useState("none");

  return <AppContext.Provider value={value}>// ...</AppContext.Provider>;
}
```

- `defaultValue` 트리 안에 적절한 `provider`를 찾지 못했을 때 쓰이는 값이다.
- 해당 `store`가 어떠한 `provider`에 할당되지 않은 경우, 완전 독립적인 `context`를 유지할때 쓰인다.
- `provider`를 통해 `undefined`를 보낸다 해도 해당 `context`를 가진 컴포넌트는 `provider`를 읽지 못합니다.
- `() => {}`의 형태는 함수의 형태를 나타낸다.
- 타입에서는 `React.SetStateAction`이 쓰이지만 이 또한 함수이기 때문에 함수의 형태를 대입한다.

### vs Redux

- 전역 상태를 사용한다는 것에서 `Redux`와 유사하며, `Redux`를 `Context`로 대체하려는 시도도 흔하게 볼 수 있다.
- `class`기반의 `Provider, Consumer`를 사용하던 때보다, `useContext, useReducer` 훅을 사용할 수 있게 됨에 따라 사용하기에 더욱 편리해졌다.
- 차이점을 몇 가지 적어보자면 다음과 같다.

1. `Redux`에서 객체 형태로 관리하는 `Store`가 없다.
2. `Redux`와 동일하게 `devTool`이 제공되지만, `Redux`와 달리 `history, action, state` 변화에 대해서는 확인할 수 없다.
3. `Redux`가 `action, reducer, dispatch`를 통해 특정 컴포넌트에서 개별 동작으로 부분 업데이트를 할 수 있는 반면, `Context`가 변경될 시, 이를 사용하고 있는 모든 컴포넌트에서 리렌더링이 발생한다.
4. `middleware`로 사이드 이펙트를 관리할 수 없다.

#### 결론

- 블로그 글들을 둘러보면 일반적으로, `Context`는 간단한 값들에 대해서 사용하고, `Redux` 모든 범위에서 `store`로 관리할 수 있는 값들이 필요한 경우나, 업데이트가 자주 발생하는 경우, 비동기 처리가 필요한 경우(redux-saga) 주로 사용한다고 설명한다.
