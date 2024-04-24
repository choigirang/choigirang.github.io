---
layout: post
title: Next 21장 - RTK(redux-toolkit), redux-persist 사용하기 (3)
author: admin
date: 2024-04-18 00:00:00 +900
lastmod: 2024-04-24 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [NEXT]
tags: [next, react, ssr, redux, rtk, persist]
---

# Next

- version 14

## Error

- Next v13에서 14로 리팩토링 하던 와중에 발생한 에러
  `redux-persist failed to create sync storage. falling back to noop storage.`

- `redux-persist`가 동기화된 스토리지를 생성하지 못하고 `noop`으로 대체된 것을 의미한다.
- **createWebStorage**는 클라이언트 측에서 사용하는 로컬 스토리지나 세션 스토리지를 서버 환경과 동일하게 설정해준다.
- 서버 환경에서는 로컬 스토리지나 세션 스토리지를 사용할 수 없기 때문에 항상 가짜 `null` 값을 반환하는 가짜 스토리지를 생성시켜준다.

```jsx
export function createPersistStore() {
  const isServer = typeof window === "undefined";
  if (isServer) {
    return {
      getItem() {
        return Promise.resolve(null);
      },
      setItem() {
        return Promise.resolve();
      },
      removeItem() {
        return Promise.resolve();
      },
    };
  }
  return createWebStorage("local");
}
const storage =
  typeof window !== "undefined"
    ? createWebStorage("local")
    : createPersistStore();

const persistConfig = {
  key: "root",
  storage,
  whiteList: ["savedMovieSlice", "userSlice"],
};
```

# 2024-04-23

## redux data

- `Redux PersistGate`를 사용 시, 하위 컴포넌트들이 모두 자바스크립트로 실행된다.
- `Provider`를 클라이언트 컴포넌트로 분리하여 적용했음에도 `persistGate`를 사용하면 자바스크립트로 실행되는데, 이에 대한 글을 찾아보니 기본적으로 `persist`는 클라이언트측에 맞게 설계됐기 때문에 SSR 적용이 매우 어렵다고 한다.
- 이렇다 할 해결책이 나와있지도 않았기에 생각한 것이 `persist`를 사용해야 하는 페이지를 분리한 뒤, `Provider`를 최소한으로 적용하는 것이었다.
- 기존 `layout` 파일에 적용하지 않고 아래와 같이 적용했다.

```
- app
  - example
    - page.tsx
    - ...etc components
```

```jsx
// redux provider.tsx
export default function PersistProvider({children} : {children: React.ReactNode}){
  return (
    <PersistGate>
      {children}
    </PersistGate>
  )
}

// page.tsx
export default function Index(){
  return(
    <React.Fragment>
    <FirstComponents/>
    <SecondsComponents/>
    <PersistProvider><ThirdComponents/></PersistProvider></React.Fragment>
  )
}

export default function ThirdComponents(){
  // persist data 사용 가능
}
```
