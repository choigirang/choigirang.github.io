---
layout: post
title: Next 14장 - redux와 localstorage
author: admin
date: 2023-08-21 00:00:00 +900
lastmod: 2023-08-21 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [NEXT]
tags: [next, react, localstorage, redux]
---

# Next

## Next.js

- 이전 글의 후속
- Next.js는 SSR 방식, Redux는 CSR 방식이기 때문에 Redux를 효과적으로 사용하기 위해서는 렌더링 방식의 통합이 필요했다.
- 이에 `next-redux-wrapper`, `redux-persist`의 옵션 추가 등의 방법이 있다고는 알게 되었지만 여러 시도 끝에 잠시 보류를 결정하고 프로젝트 진행을 위한 다른 방식을 택했다.
- 브라우저가 종료되거나 새로고침 시 `redux`의 데이터가 초기값으로 돌아가는데, 이는 데이터가 단기적으로 유지되는 특성과 브라우저 이동이나 페이지간 이동에 대한 상태 저장은 자체적으로 처리하지 않기 때문이라고 한다.
- 그렇다면 데이터를 유지하기 위해선 어떤 방법이 있을까
  1. 이전 글과 같이 `redux-persist`를 사용하는 방법이 있다.
  2. `localStorage, sessionStorage`를 사용하는 방법이 있다.
  3. 서버에 데이터를 저장하여 처리하는 방식이 있다.
- 위 3가지 경우의 수에 대해 고민했고 처음엔 `redux-persist`를 사용하려 했지만 `SSR` 방식의 충돌과 Next.js에서의 사용 방식에 대한 정보가 적어 2번과 3번의 방식으로 선택하기로 했다.
- 2번의 처리 방식으로 생각한 것은, 활용하고자 하는 데이터를 로컬 스토리지에 저장해놓았다가 `redux`가 초기화 됐을 때 필요한 데이터를 저장된 `localStorage`에서 받아와 다시 상태를 변경하는 것이다.
- 3번도 비슷한 방식이지만, `redux`의 상태가 초기화 됐을 떄 토큰을 활용하여 서버에 데이터를 요청하고, 서버에서 보내준 데이터로 다시 상태를 변경하는 방식을 생각했다.
- 서버의 데이터 요청을 줄일 수 있다는 생각에 2번을 시도해보았다.

### Login

- 기존에는 로그인 되었을 때, 서버에서 보내준 유저 데이터를 저장하는 방식을 사용했다.

```js
// redux
const dispatch = useDispatch();

// api instance
// 입력된 상태값 전송
api.get("/login", {id: user.id, password: user.password})
.then(res => {
    // 로그인 데이터 redux
    dispatch(loginSuccess(res.data.user));
    })...
```

- 여기서 새로고침 시 초기화 되는 데이터를 유지하기위해 저장된 데이터를 `localStorage`에 넣어놓는다.
- 그리고 초기화 될 떄 `localStorage`에 데이터가 있다면 이를 활용한다.

```js
const saveLoginData = (user: LoginData) => {
  localStorage.setItem("user", user);
};

// 로그아웃 시 로컬 데이터 삭제
const deleteLoginData = () => {
  localStorage.removeItem("user");
};

// redux의 reducer
const user = useSelect((state: RootState) => state.user);

// 위와 동일
useEffect(() => {
  // redux에 저장된 로그인이 초깃값으로 돌아가서 데이터가 없을 시
  if (!user.login) {
    const getLocalUser = localStorage.getItem("user");
    // 저장된 유저 데이터 parse처리
    dispatch(loginSuccess(JSON.parse(getLocalUser)));
  } else {
    // 저장된 데이터가 없다면
    alert("로그인이 필요합니다.");
    // 홈으로 이동
    router.push("/");
  }
}, []);
```

### App

- 위와 같이 코드를 작성했다.
- 다소 복잡하고 분명 더 좋은 방법이 있으리라 생각되지만, 우선 기능 구현을 위해 진행하기로 했다.
- 위와 같이 사용된 `Login`컴포넌트에 대한 문제점이 있었다.
- `Login`컴포넌트가 모든 페이지에 쓰이는 것이 아니기 떄문에, 컴포넌트가 존재하지 않는 페이지로 이동했을 시에는 `redux`가 다시 초기값으로 되돌아간다는 문제였다.
- 따라서 이를 최상위 컴포넌트인 `App` 컴포넌트에서 사용하려고 했다.

```js
function App() {
  // ...생략

  const user = useSelect((state: RootState) => state.user);
  const dispatch = useDispatch();

  useEffect(() => {
    //...동일
  }, []);

  return (
    <CookiesProvider>
      <Provider store={store}>
        <QueryClientProvider client={client}>
          <Component {...pages} />
        </QueryClientProvider>
      </Provider>
    </CookiesProvider>
  );
}
```

- 여기서 에러가 발생했는데, 당연하게도 `Provider`로 `redux`의 상태값을 전역적으로 활용하기 전에 `useSelect, useDispatch`를 사용하는 코드가 먼저 읽히기 때문에 동작하지 않았다.
- 이를 위해서 `Component` 컴포넌트를 감쌀 하나의 컴포넌트르 만들어, `redux`를 사용하기로 했다.

```js
// App 동일
// return을 제외한 redux 코드 삭제
function LoginState({ children }: { children: ReactNode }) {
  const user = useSelect((state: RootState) => state.user);
  const dispatch = useDispatch();

  useEffect(() => {
    //...동일
  }, []);

  return <>{children}</>;
  // <React.Fragment> 시 에러
  // <> === <React.Fragment> 라고 생각했는데 에러가 발생
}

function App() {
  // ...생략

  const user = useSelect((state: RootState) => state.user);
  const dispatch = useDispatch();

  useEffect(() => {
    //...동일
  }, []);

  return (
    <CookiesProvider>
      <Provider store={store}>
        <QueryClientProvider client={client}>
          <LoginState>
            <Component {...pages} />
          </LoginState>
        </QueryClientProvider>
      </Provider>
    </CookiesProvider>
  );
}
```

- 이렇게 작성하면 정상적으로 동작했다.
- 여기서도 개념 숙지가 제대로 되지 않았던 `children`에 대해 알게 됐다.

### props children

- 컴포넌트가 어떠한 컴포넌트를 자식 요소로 갖으려 할 때 발생하는 타입 에러이다.
- 컴포넌트로 작성한 `function`조차도 변수라고 생각하면 되는데, 부모 컴포넌트로 종속되는 컴포넌트들을 사용하기 위해선 타입 정의를 해줘야한다.
- 예를 들어,

```jsx
<div>아래의 컴포넌트와 똑같다.</div>

<ParentComponent><ChildCompoent/></ParentComponent>
```

- 이처럼 어떠한 컴포넌트로 종속되기 위해서는 타입을 정의해줘야 한다는 것이다.
- 이 때, 종속되는 컴포넌트의 유형은 `ReactNode` 타입이기 떄문에, 전달되는 컴포넌트의 `props`가 `children`이 되는 것이고, `children`의 타입을 `ReactNode`로 정의해주어 해결이 가능했다.
