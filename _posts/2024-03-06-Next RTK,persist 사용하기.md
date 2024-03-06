---
layout: post
title: Next 19장 - RTK(redux-toolkit), redux-persist 사용하기 (1)
author: admin
date: 2024-03-06 00:00:00 +900
lastmod: 2024-05-06 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [NEXT]
tags: [next, react, ssr, redux, rtk, persist, hydrate]
---

# Next

- SSR 환경에서 리덕스를 사용할 때에는 추가 작업이 필요하다.
- 서버 사이드 환경에서 스토어를 생성하고, 이후 클라이언트 사이드 환경에서 나머지 로직들을 수행하며 만들어진 스토어가 다르기 때문에 이 둘을 합쳐주는 과정이 필요하다.
- 또한 유저가 페이지를 이동하며 페이지를 요청할 때마다 스토어가 생성되기 때문에 리덕스 스토어가 여러 개 생성될 수 있다.
- 그렇기에 이를 통합시켜주기 위한 `next-redux-wrapper` 라이브러리를 사용해야 한다.

## Next-redux-wrapper

```
npm i next-redux-wrapper
```

### Hydrate

[참고자료](https://velog.io/@hamjw0122/Next.js-Hydration)

- 라이브러리를 사용하기 위해 `HYDRATE`라는 개념을 알아야한다.
- 서버 사이드 렌더링에서 렌더링 된 페이지와 자바스크립트를 클라이언트에게 보내면, 클라이언트에서 HTML 코드와 자바스크립트를 매칭시키는 과정을 말한다.
- 자바스크립트 코드들이 DOM 요소 위에 물을 채우듯 필요한 요소들을 채운다하여 붙여진 이름이다.
- 사용자는 이러한 과정을 통해 UI를 미리 보고, 이후 **Hydration**을 통해 페이지와 상호 작용이 가능하다.

#### Hydration 과정

1. 리액트로 페이지를 제작한다.
2. Next나 Gatsby와 같은 프레임 워크는 리액트에서 HTML을 생성하기 위한 `React Server Side API`인 **ReactDOMServer**를 사용해 제작된 사이트의 프로덕션 단계 파일을 생성한다.
3. 이때 보게되는 웹 페이지는 서버에서 생성된 정적인 HTML파일이다.
4. 페이지가 로딩된 이후, 자바스크립트가 로드되고, **ReactDOM.hybrate()**는 자바스크립트와 함께 서버에서 렌더링되었던 HTML 페이지에 수분을 공급한다.
5. `Hydration`이후 `React reconciler API`가 자리를 대체하고 상호작용이 가능해진다.

- **Hydration은 Next만의 특별한 동작이 아니라 ReactDOM 함수이다.**

### HYDRATE action

- `reducer`의 HYDRATE 액션을 정의한다.
- 서버에서 클라이언트로 상태를 전달할 때, 서버에서 렌더링된 페이지의 상태를 클라이언트로 그대로 전달하여 클라이언트에서도 상태를 유지하는 것이다.
- 서버에서 전송한 초기 상태를 클라이언트에서 재사용하는데, 리듀서의 클라이언트를 새로 쓰는 액션 핸들러이다.

```jsx
// store.ts
export const reducer = (state, action) => {
    // HYDRATE가 발생할 때, 상태값을 그대로 전해준다.
  if (action.type === HYDRATE) {
    return {
      ...state,
      ...action.payload,
    };
  }
  return combineReducers({
    ...
  })(state, action);
};

const makeStore = () =>
  configureStore({
    reducer,
    middleware: [
      ...getDefaultMiddleware({ thunk: true, serializableCheck: false }),
    ],
  });
```

### createWrapper()

- 스토어를 앱에 제공하기 위한 `wrapper`이다.
- 서버와 클라이언트 간에 리덕스 스토어를 공유하고 상태를 유지하는 역할을 한다.

```jsx
// store.ts

...

export const wrapper = createWrapper(makeStore, ...)
```

```jsx
// _app.tsx

function App(){
    ...
}

export default wrapper.withRedux(App)
```
