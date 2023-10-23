---
layout: post
title: React 62장 - React Context (3) with TS
author: admin
date: 2023-10-23 00:00:00 +900
lastmod: 2023-10-23  00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags:
  [
    react,
    components,
    jsx,
    query,
    context,
    redux,
    recoil,
    state,
    tsx,
    typescript,
  ]
---

# React

## Context API (with Typescript)

- `Context API`를 타입스크립트와 함께 사용하려면 어떻게 해야할까.
- 렌더링 최적화를 위해 여러 개의 `Context`를 묶어 사용하고, 상태 끌어올리기로 `children`으로 넘겨받을 하위 요소들에 대한 타입 정의가 필요했다.

### AppProvider

- `App`컴포넌트에서 여러 개의 `Context`를 넘겨 사용할 `AppProvider`

```tsx
type ContextProviderProps<T> = {
  contexts: React.FC<T>[];
  children: React.ReactNode;
};

const AppProvider: React.FC<ContextProviderProps<any>> = ({
  contexts,
  children,
}) => {
  return (
    <>
      {contexts.reduce((prev, context) => {
        return React.createElement(context, { children: prev });
      }, children)}
    </>
  );
};
```

### Context

- 사용하고자 하는 목적과 값에 따른 `Context`

```tsx
type ExampleContextType = {
  example: string;
  setExample: React.Dispatch<React.SetStateAction<string>>;
};

const ExampleContext = createContext<ExampleContextType>({
  example: "",
  setExample: () => {},
});

const ExampleProvider = ({ children }: { children: ReactNode }) => {
  const [example, setExample] = useState("");

  return (
    <ExampleContext.Provider value={{ example, setExample }}>
      {children}
    </ExampleContext.Provider>
  );
};
```

#### 정리

##### 컨텍스트 만들기

- 우선 작성한 구조는 위의 예시 코드들과 같다.
- 처음부터 애먹었던 것은 타입스크립트를 사용하여 컨텐스트를 만들 때에는 `createContext` 안에 `defaultValue`가 필수적이라는 차이를 이해하기 어려웠다.
- [공식문서](https://react.dev/reference/react/createContext#createcontext)
- 공식문서의 `defaultValue`에 대한 설명을 읽어보면, `React가 해당 Context object를 구독하고 있는 컴포넌트를 render할 때 Context value를 가장 가까운 Provider에서 찾게 되는데 Provider가 존재하지 않는 경우에만 default 값이 적용된다.`
- 이 설명이 잘 이해되지 않았던 것은 **Provider**의 역할에 대한 이해도가 낮아서인가보다.
- 이해한 설명은 다음과 같다.

  1. `Provider`는 하위 컴포넌트들이 컨텍스트 값을 사용할 수 있도록 제공하는 역할을 한다.
  2. 따라서 `createContext`로 만든 컨텍스트가 사용할 수 있는 컴포넌트인 `Provider`를 사용하여, 값을 사용하고자 하는 컴포넌트들에게 값을 제공하는 역할자이다.
  3. 위의 예시 코드처럼 우리가 값을 변경하거나, 변경한 값이 저장돼있는 `example, setExample`이 `ExampleProvider`라는 컴포넌트에서 동작하고 있지만, **만약 이 컴포넌트가 없다면 컨텍스트를 생성할 때 입력해준 defaultValue가 기본값으로 들어간다는 것이다.**

- 그렇기 때문에, `createContext`를 사용하여 컨텍스트를 만들어줄 때 `Provider`로 넘겨줄 `value` 또한 고려하여 최초의 올바른 타입을 정의해줘야 한다.

##### 상태 끌어올리기 타입

- 컨텍스트를 만들어줬다면 상태끌어올리기를 통해 이를 최상단에서 사용할 `AppProvider`를 만들 때의 타입이다.
- 최상위 컴포넌트인 `App`에서 사용하고자 하는 컨텍스트를 `AppProvider`의 `props`로 넘겨준다.
- 위에 적은 `AppProvider`의 예시 코드를 보면, `contexts`에는 우리가 만들어서 넣어줄 여러 개의 컨텍스트를, `children`은 하위 요소들을 의미한다.
- 여기서는 `contexts`의 타입을 정의하는 것에 애를 먹었다.
- **createElement**와 **FC**타입에 대한 이해도 부족이라는 생각이 들었다.
- [JSX 타입스크립트](https://www.typescriptlang.org/ko/docs/handbook/jsx.html)
- [createElement](https://lakelouise.tistory.com/84)
- [FC type](https://velog.io/@deli-ght/React.FC-vs-JSX.Element)
- [제네릭 타입](https://joshua1988.github.io/ts/guide/generics.html#%EC%A0%9C%EB%84%A4%EB%A6%AD%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0)
- 위 네개의 게시글을 읽어보고 이해한 바는 다음과 같다.
  1. 우선 우리가 사용하는 컴포넌트인 함수형 컴포넌트는, 클래스형 컴포넌트와 다르기 때문에 `FC type`이다.
  2. `contexts`타입 정의를 여러 번 시도하며, `FC type`으로 이루어진 배열을 `props`로 전달받는다.
  3. `React.createElement`로 만들어진 `JSX.Element`는 기본적으로 `any`타입의 `props`와 `type`을 갖기 때문에 제네릭타입으로 `any`타입을 전달한다.
