---
layout: post
title: React 72장 - Redux-Toolkit (2) 세팅하기
author: admin
date: 2024-03-05 00:00:00 +900
lastmod: 2024-03-05  00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, redux, rtk, toolkit, state]
---

# React

## Redux-Toolkit (RTK)

### store

```
- store
    - slice (reducer files)
        - ...
        - ...
    store.ts
```

### store.ts

1. `rootReducer` : 생성한 여러 개의 리듀서들을 하나로 통합한다.
2. `store` : 전역적으로 사용할 하나의 스토어를 만든다.
3. 이하 타입들 : `hooks`에서 사용할 타입들 (추후 설명)

```jsx
const rootReducer = combineReducers({
    // 슬라이스들 통합
    exampleSlice: exampleSlice.reducer
})

export const store = configureStore({
    reducer: rootReducer,
    middleWare: ...
})

export type AppStore = typeof store
export type RootState = typeof store.getState
export type AppDispatch = typeof store.dispatch;
```

#### slice/example.ts

- `PayloadAction` : 타입 지정
- `createSlice` : 생성한 `reducers`의 액션 `export`
  - `dispatch` 사용

```jsx
type InitState = {
  example: string,
};

export const initialState: InitState = {
  example: "예시입니다.",
};

export const exampleSlice = createSlice({
  name: "example",
  initialState,
  reducers: {
    changeMsg: (state: InitState, action: PayloadAction<InitState>) => ({
      example: action.payload.example,
    }),
    resetMsg: () => initialState,
  },
});

export const { changeMsg, resetMsg } = exampleSlice.actions;
export default exampleSlice;
```

### hooks/useRedux

- `useAppDispatch` : `useDispatch` 직접 사용 시, `thunkAction`타입에러 발생
- `useAppSelector` : `useSelector` 직접 사용 시, 매번 `state` 타입을 지정해줘야 하기 떄문에 `RootState`로 커스터 마이징

```jsx
import { useDispatch, useSelector } from "react-redux";
import type { TypedUseSelectorHook } from "react-redux";
import type { RootState, AppDispatch } from "../store/store";

export const useAppDispatch: () => AppDispatch = useDispatch;
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;
```
