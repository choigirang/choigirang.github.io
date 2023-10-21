---
layout: post
title: React 61장 - React Context (2)
author: admin
date: 2023-10-18 00:00:00 +900
lastmod: 2023-10-21  00:00:00 +900
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

#### 방법 1. 값을 별도의 객체로 관리 (PASS)

[참고자료](https://as-you-say.tistory.com/215)

- 객체를 통째로 상태값으로 관리하게 되면, 상태 객체가 변경되지 않는 경우에는 하위의 컴포넌트가 불필요하게 리렌더링 되지 않는다고 한다.
- 잘 이해를 못 하겠다...상태 객체는 어차피 변경될 여지로 만들어 두는 게 아닌가...? 어떻게 하면 리렌더링이 일어나지 않을까 여러 방법으로 시도해보았지만, 위에서 설명하는 리렌더링이 안 일어나는 방법은 뭔지를 모르겠다.
- 예시 1번 코드를 예시 2번 코드로 바꿔서 얻는 이점이 뭘까...모르겠다.

**예시 1**

```jsx
const UserContext = createContext({ username: "unknown", age: 0 });

export default function App() {
  const [username, setUsername] = useState("");
  const [age, setAge] = useState(0);

  return (
    <div>
      <UserContext.Provider value={(username, age)}>
        <Profile />
      </UserContext.Provider>
    </div>
  );
}
```

**예시 2**

```jsx
const UserContext = createContext({ username: "unknown", age: 0 });

export default function App() {
  const [user, setuser] = useState({ username: "horong", age: 23 });

  return (
    <div>
      <UserContext.Provider value={user}>
        <Profile />
      </UserContext.Provider>
    </div>
  );
}
```

#### 방법 2. React.memo 사용하기

[참고자료](https://hong-jh.tistory.com/entry/Context-API%EB%8A%94-%EC%99%9C-%EC%93%B0%EA%B3%A0-%EA%B7%B8%EB%A0%87%EB%8B%A4%EB%A9%B4-Redux%EB%8A%94-%ED%95%84%EC%9A%94%EC%97%86%EC%9D%84%EA%B9%8C)
[React.memo](https://ui.toast.com/weekly-pick/ko_20190731)

- 컴포넌트를 렌더링 할 때 사용하는 `React.memo`를 사용한다.
- `memo`는 컴포넌트를 렌더링한 뒤, 이전 렌더링 결과와 다르면 업데이트를 하는데, `Context`를 사용하여 값을 변경한다 해도, 이를 사용하는 컴포넌트가 아닌 이상 컴포넌트의 변경점은 없다.
- 따라서 불필요한 리렌더링이 발생하지 않는다.

```jsx
type ExampleType = {
  example: string,
  setExample: React.Dispatch<React.SetStateAction<string>>,
};

const ExampleContext = createContext({
  example: "",
  setExample: () => {},
});

const App = () => {
  const [example, setExample] = useState();

  return (
    <Example.Provider value={{ example, setExample }}>
      <ChildOne />
      <ChildTwo />
    </Example.Provider>
  );
};

const ChildOne = () => {
  console.log("ChildOne component render");
  return null;
};

const ChildTwo = () => {
  console.log("ChildTwo component render");
  const { example, setExample } = useContext(ExampleContext);

  return <button onClick={() => setExample("changed")}>example 변경</button>;
};
```

- 위와 같은 코드가 있다면 콘솔에 컴포넌트 1,2에 해당하는 콘솔 메시지가 전부 찍혀있을 것이다.
- `ChildTwo`에서 버튼을 사용하여 `example`값을 변경할 때, 값을 사용하고 있지 않은 `ChildOne` 컴포넌트에 대한 불필요한 리렌더링이 발생하기 때문에 콘솔 메시지가 찍히는 것을 볼 수 있다.
- 이는 컴포넌트의 상태가 변경되면 해당 컴포넌트의 하위 컴포넌트도 리렌더링 되는 컴포넌트의 특성때문이다.
- `ChildOne`은 `example`의 상태값을 전혀 사용하고 있지 않지만, `App`컴포넌트의 하위 컴포넌트에 속하기 때문에, 설령 값을 사용하지 않고, `ChildTwo`컴포넌트에서 값을 변경하더라도 `ChildOne`컴포넌트에 대한 불필요한 리렌더링이 발생한다.
- 이를 방지하기 위해 `React.memo`를 사용하여 컴포넌트의 리렌더링을 방지할 수 있다.

```JSX
const ChildOne = React.memo(() => {
  console.log("ChildOne component render")
  return null;
});
```

- `ChildOne` 컴포넌트를 `React.memo`로 감싸 리렌더링을 방지할 수 있는데, `React.memo`는 순수함수를 상속받는 것과 같이 동작한다고 한다.
- 처음 렌더링 될 때 콘솔에 `"ChildOne component render"`가 찍히고, `ChildTwo`에서 상태값을 변경하더라도 `ChildOne` 컴포넌트에 대한 변경점이 없기 때문에 리렌더링이 발생하지 않는다.

```jsx
const ChildTwo = () => {
  const { example, setExample } = useContext(ExampleContext);
  console.log("ChildTwo component render");

  return (
    <React.Fragment>
      <ChildThree />
      <button onClick={() => setExample("changed")}>example 변경</button>
    </React.Fragment>
  );
};

const ChildThree = () => {
  console.log("ChildThree component render");

  return null;
};
```

- 만약 `ChildTwo` 컴포넌트의 하위 컴포는트로 `ChildThree` 컴포넌트가 있다면 어떨까.
- `ChildTwo`에서 상태값을 변경할 때, 상태값을 사용하고 있지 않은 `ChildThree` 컴포넌트에 대한 리렌더링도 똑같이 발생한다.
- 이는 위에서 설명한 컴포넌트의 특성때문에 `ChildTwo` 컴포넌트가 리렌더링 되면서 하위 컴포넌트들 또한 리렌더링 시키기 때문이다.

```jsx
const ChildThree = React.memo(() => {
  ...
})
```

- 똑같이 `React.memo`를 사용하여 리렌더링을 방지할 수 있지만, 매 컴포넌트마다 `React.memo`를 기본값으로 작성해주는 것도 번거로운 일이다.
- 리렌더링이 발생하는 컴포넌트에 대한 정리를 통해서 이러한 과정을 최소화 할 수 있을 거라 생각하지만, 더 좋은 방법이 있는지에 대해서도 생각해야겠다.

#### 방법 2. useMemo 사용하기 (PASS)

[참고자료](https://velog.io/@nemo/context-useMemo)

- `useMemo`를 활용하여 리렌더링을 방지하는 방법에 대해 설명하고 있다.
- 직접 사용하여 실험해보고 있지만 리렌더링은 똑같이 발생한다.
- 의문이 들었던 것은 `useMemo`로 값을 기억할 수 있지만, `Context`에서 사용하는 값이 변경될 때면 메모이제이션이 새로 발생하며 리렌더링이 당연히 일어나게 되는 것 아닐까? 이 방법으로 어떻게 리렌더링을 최적화하는 것인지 의문이다.
