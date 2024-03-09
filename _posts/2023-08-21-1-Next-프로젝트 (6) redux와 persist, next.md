---
layout: post
title: Next 13장 - persist, redux
author: admin
date: 2023-08-21 00:00:00 +900
lastmod: 2023-08-21 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [NEXT]
tags: [next, react, persist, csr, ssr, redux]
---

# Next

## Next.js

- Next.js는 React 라이브러리 프레임워크이다.
- React가 `Client Side Rendering (CSR)`을 하기에, 페이지를 그릴 떄 빈 html을 가져와 script를 로딩하기 떄문에 시간이 오래 걸리고 `Serch Engine Optimiztion(SEO)`에도 취약하다는 단점이 있다.
- 반면, Next.js는 `pre-reloading`을 통해 미리 데이터가 렌더링된 페이지를 가져올 수 있게 해줄 수 있어 사용자에게 더 좋은 경험을 제공하게 된다.
- `Server Side Rendering (SSR)`과 `SEO`뿐만 아니라 `CSR` 또한 혼합하여 사용이 가능하다.

### redux-persist

- `redux-persist`는 Redux의 상태를 로컬 스토리지나 세션 스토리지에 저장하고 관리하는 라이브러리로, 새로고침을 하거나 앱을 다시 시작할 때도 상태를 보존할 수 있도록 해준다.
  - `localStorage` : 영구 저장소
  - `sessionStorage` : 임시 저장소
  - 데이터의 중요도나 쓰임에 따라 구분하여 사용할 수 있다.
- `Login`에서 사용자의 정보를 Redux에 저장하여 사용하고자 해도, 새로고침을 하면 저장된 사용자의 정보가 초기화 된다.
  - 토큰을 이용해 저장된 토큰으로 매번 사용자의 정보를 요청할 수도 있다.

#### redux-persist 사용법

- 쿠키와 다르게 서버에 따로 요청을 보내지 않아 효율적이며, 이러한 특징때문에 더 많은 정보들을 저장할 수 있다.
- 개발자가 브라우저 내 웹 스토리지 구성 방식을 설정할 수 있다는 유연한 장점과 서버가 HTTP 헤더를 통해 스토리지 객체를 조작할 수 없다는 특징이 있다.

##### store 정의

```js
// reducers/index.js
import { combineReducers } from "redux";
➊ import { persistReducer } from "redux-persist";
➋ import storage from "redux-persist/lib/storage";

import auth from "./auth";
import board from "./board";
import studio from "./studio";

➌ const persistConfig = {
  key: "root",
  // localStorage에 저장합니다.
  storage,
  // auth, board, studio 3개의 reducer 중에 auth reducer만 localstorage에 저장합니다.
  whitelist: ["auth"]
  // blacklist -> 그것만 제외합니다
};

export const rootReducer = combineReducers({
  auth,
  board,
  studio
});

➍ export default persistReducer(persistConfig, rootReducer);
```

- Redux의 사용법에 추가된다.
- `reducer`를 설정하고, 어느 지점에서부터 저장할 건지 `key`를 작성, 저장하고자 하는 `reducer`의 데이터만 저장하거나 제외할 수 있다.

##### App 사용

- Redux가 최상위 컴포넌트인 `App`에서 `Provider`를 사용하는 것과 마찬가지로

```js
// src/index.js

import React from "react";
import ReactDOM from "react-dom";
import { createStore, applyMiddleware, compose } from "redux";
import { Provider } from "react-redux";
➊ import { persistStore } from "redux-persist";
➋ import { PersistGate } from "redux-persist/integration/react";
import App from "./App";
import configureStore from "./store";
import { rootReducer } from "./reducers";

const store = createStore(rootReducer);
➌ const persistor = persistStore(store);

const Root = () => (
  <Provider store={store}>
    ➍ <PersistGate loading={null} persistor={persistor}>
      <App />
    </PersistGate>
  </Provider>
);

ReactDOM.render(<Root />, document.getElementById("root"));
```

- 추가된 `rootReducer`로 `store`를 생성하고, 새로고침을 하거나 종료를 해도 유지될 `persistor`를 설정한다.
- `PersistGate`로 `persist store`가 Redux에 저장될 때까지 앱 UI렌더링이 지연된다.

### Next.js와 redux-persist

- Next.js에서 redux-persist를 사용하는 것은 비효율적이라고 한다.
- `redux-persist`를 사용하면 클라이언트 측에서 저장된 데이터를 사용하는 것이고, Next.js는 서버를 사용하는 것이다.
- 그렇다면 서버에서 보내서 펼쳐준 데이터와 클라이언트 측에서 갖고 있는 데이터가 불일치할 수 있는 경우가 생긴다.
- 이를 위해 `redux-persist`의 옵션 중 SSR을 사용하는 방법을 알아보자.

### next-redux-wrapper

**자료가 충분치 않아 보류🥲**

### 2024.03.06

[next-redux-wrapper](<https://choigirang.github.io/posts/Next-RTK,persist-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0(2)/>)
