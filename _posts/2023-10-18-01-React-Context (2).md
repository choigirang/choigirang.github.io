---
layout: post
title: React 61장 - React Context (2)
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

### 사용하다보니 메모

- `Context`를 사용하다보면 하나의 상태값을 여러 컴포넌트에서 사용하기 때문에, 상태값을 사용하고 있는 여러 컴포넌트에서 불필요한 리렌더가 발생할 수 있다고 한다.
- 예를 들어, `example`이라는 값을 `const {example} = useContext(ExampleContext)`로 두 개의 컴포넌트에서 사용하고 있다.
- `A` 컴포넌트에서 값을 변경했어도, 이 값을 사용하고 있는 `B` 컴포넌트에서도 리렌더링이 발생한다.

```tsx
type DefaultValue = {
  example: string;
  setExample: React.Dispatch<React.SetStateAction<string>>;
};

const AppContext = createContext<DefaultValue>({
  example: "",
  setExample: () => {},
});

function App() {
  const [example, setExample] = useState("");

  return (
    <AppContext.Provider value={{ example, setExample }}>
      <ExampleHeader />
      <ExampleBody />
    </AppContext.Provider>
  );
}
```

- 위와 같은 코드에서 `ExampleBody` 컴포넌트에서 `example`값을 변경한다고 가정하면, 최상위 컴포넌트에 포함돼있는 `ExampleHeader`에서도 리렌더링이 발생한다.

```tsx
function ExampleHeader(){
    console.log(1);

    return(
        ...
    )
};

function ExampleBody(){
    const {setExample} = useContext(AppContext);

    // example을 변경하는 핸들러
    function changeExampleHandler(e:React.EventHandler){
        setExample(e.target.value);
    }
    return (
        // example 변경
    )
};
```

- `example`값이 변경될 때마다 `console`에 1이 찍히며, 불필요한 리렌더링을 해결할 필요가 있었다.
