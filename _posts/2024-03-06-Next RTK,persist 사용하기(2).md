---
layout: post
title: Next 20장 - RTK(redux-toolkit), redux-persist 사용하기 (2)
author: admin
date: 2024-03-06 00:00:00 +900
lastmod: 2024-03-06 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [NEXT]
tags: [next, react, ssr, redux, rtk, persist]
---

# Next

- `next-redux-wrapper`를 통해 클라이언트와 서버 간의 상태 공유는 끝났다.
- next에서는 동적 페이지를 사용하기 때문에 페이지 전환이 빈번하게 발생한다.
- 페이지 전환이나 새로고침 시에는 리덕스의 상태가 초기화 되는데, 상태를 유지시켜주기 위해 `redux-persist`를 사용할 수 있다.
  - `localStorage`를 사용할 수도 있다.

## redux-persist

```
npm i redux-persist
```

- 우선 `persistReducer` 함수가 존재한다.
- 위 함수는 리듀서를 감싸고, 저장소 및 키값을 정하여 지정한 저장소에 데이터를 저장한다.
- `persistStore`로 상태가 초기화 되어도 저장된 데이터를 통해 스토어의 상태로 사용한다.

### persistConfig & persistReducer

1. `persistConfig` : 데이터가 저장될 위치와 키값을 정한다.

- `whiteList` : 저장될 특정 리듀서의 명칭을 지정한다.
- `blackList` : 제외될 특정 리듀서의 명칭을 지정한다.

2. `persistReducer` : 리듀서와 작성된 `config`를 바탕으로 데이터를 저장한다.
3. `store` : `persistReducer`로 작성된 리듀서로 스토어를 생성한다.

```jsx
import { persistReducer } from 'redux-persist';
import storage from 'redux-persist/lib/storage';

const persistConfig = {
    key: "root",
    storage,
    // whiteList : [],
    // blackList : [],
}

const reducer = (state: ..., action: PayloadAction<...>) => {
    if (...)

    return combineReducers({
        ...
    })
}

const persistedReducer = persistReducer(persistConfig, reducer)

export const store = configureStore({
    reducer: persistedReducer,
    ...middleWare
})
```

```jsx
// exampleSlice.ts

export const saveExampleSlice = createSlice({
    name: "saveExample",
    ...
})

export const exceptExampleSlice = createSlice({
    name: "exceptExample",
    ...
})

// store.ts
const persistConfig = {
    ...,
    whiteList: ["saveExample"], // saveExampleSlice에서 발생하는 데이터만을 저장한다.
    blackList: ["exceptExample"] // exceptExampleSlice에서 발생하는 데이터만을 제외한다.
    // 위의 작성된 두 가지 동작은 같다.
}
```

### persistor

- 마찬가지로 리덕스 스토어에 상태를 저장하고 복원하는 역할을 한다.
- 스토어에 상태가 영구적으로 저장되고, 저장된 상태를 복원하여 스토어를 초기화한다.

```jsx
// store.ts
...

const persistor = persistStore(store)
```

### PersistGate

- `persist`에서 제공하는 컴포넌트로, 리덕스의 상태를 영구저장소에서 불러오는 역할을 한다.
- 최상위 컴포넌트에 렌더링하고, 데이터가 초기화되는 동안의 로딩 스피너 또는 로딩화면을 표시할 수 있다.

```jsx
// _app.tsx
import { PersistGate } from "next-redux-wrapper";
import { persistor } from "../store";

function App({ Component, pageProps }: AppProps) {
  return (
    <Provider store={store}>
      <PersistGate loading={<LoadingSpinner />} persistor={persistor}>
        <Component {...pageProps} />
      </PersistGate>
    </Provider>
  );
}

export default wrapper.withRedux(App);
```
