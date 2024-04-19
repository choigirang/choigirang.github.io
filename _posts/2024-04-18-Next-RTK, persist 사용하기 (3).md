---
layout: post
title: Next 21장 - RTK(redux-toolkit), redux-persist 사용하기 (3)
author: admin
date: 2024-04-18 00:00:00 +900
lastmod: 2024-04-18 00:00:00 +900
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
