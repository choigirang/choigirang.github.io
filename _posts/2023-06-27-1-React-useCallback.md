---
layout: post
title: React 48장 - useCallback과 useMemo
author: admin
date: 2023-06-27 00:00:00 +900
lastmod: 2023-07-08 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, usecallback, usememo, memoization]
---

# React

## useMemo, useCallback

- 컴포넌트의 성능을 최적화하기 위해 사용되는 훅이다.
- 리액트에서 렌더링 => 함수 호출 => 변수 초기화의 과정에서 불필요한 리렌더링을 최소화시켜 원래 있던 값이나 함수를 재사용하여 성능을 높이기 위해 사용한다.

## useMemo

- `useMemo`는 동일한 계산을 반복해야 할 때, 계산한 값을 메모리에 저장하여 동일한 계산이 반복 수행하는 것을 방지한다.
- 동일한 값을 반환하는 계산을 반복적으로 수행해야 할 경우, 이미 저장돼있는 값을 활용하여 반복된 동작이 실행되지 않도록 한다.

```jsx
function RepeatCalc() {
  const value = calculate();
  return <div>{value}</div>;
}

function calculate() {
  return 10;
}
```

- 위 코드에서 함수의 렌더링,호출,초기화 과정이 반복되기 때문에, 컴포넌트가 렌더링 될 때마다 `value`라는 변수가 초기화되며 재호출된다.
- 만약 함수가 무겁고 복잡하다면 비효율적인 호출이 반복되는 것인데, 렌더링되더라도 `calculate`를 재호출하지 않고 저장되어있는 값을 재사용한다.
- `useMemo`는 값을 메모리에 저장시켜놓고 재사용하기 때문에 메모리에 값을 저장하는 것만으로도 메모리 사용이 발생하고, 중복되는 결과값으로 불필요한 렌더링이 일어나지 않는 경우, 사용하지 않는 것이 메모리 측면에서 효율적이다.

### useMemo 구조

- 첫 번째 인자로는 콜백함수를, 두 번째 인자로는 의존성 배열을 받는다.
- 의존성 배열 안에 있는 값이 업데이트 될 때만 콜백함수를 다시 호출하여 메모리에 저장된 값을 업데이트한다.

```jsx
const value = useMemo(() => {
  return calculate();
}, [item]);
```

### useMemo 사용 예시

```jsx
function App() {
  const [number, setNumber] = useState(0);
  const [txt, setTxt] = useState("");

  const increase = () => {
    console.log("숫자가 증가합니다.");
    setNumber(number + 1);
  };

  const decrease = () => {
    console.log("숫자가 감소합니다.");
    setNumber(number - 1);
  };

  const reset = () => {
    console.log("글자가 초기화됩니다.");
    setTxt("");
    return txt;
  };

  return (
    <div>
      <p>숫자 : {number}</p>
      <button onClick={increase}>increase</button>
      <button onClick={decrease}>decrease</button>

      <p>글자 : {txt}</p>
      <input type="text" onChage={(e) => setTxt(e.target)} />
      <button onClick={reset}>reset</button>
    </div>
  );
}
```

**삽질**

- 예시를 만들어보려고 위와 같은 코드를 짰지만, 내가 예상했던대로 상태값이 변경됨에 따라 컴포넌트가 리렌더링 되면서 실행됐어야 하지만 아무런 변화가 없었다.
- 나는 지금껏 상태를 변경하는 함수만을 작성했을 뿐이며, 컴포넌트가 리렌더링된다 해도 `button`을 통해 발생하는 실행함수이기 때문에 콘솔에 현재 실행한 함수밖에 찍히지 않았다.

### 새로운 예시

- 이와 같이 작성하면 컴포넌트가 리렌더링됨에 따라 `p`태그의 `plus10()`함수가 실행되기 때문에 상태값이 변경될 때마다 콘솔에 **그냥 값**이 찍히게 된다.
- `useMemo`를 활용해보자.

```jsx
export default function App() {
  const [number, setNumber] = useState(0);
  const [txt, setTxt] = useState("");

  const increase = () => {
    console.log("숫자가 증가합니다.");
    setNumber(number + 1);
  };

  const decrease = () => {
    console.log("숫자가 감소합니다.");
    setNumber(number - 1);
  };

  const plus10 = () => {
    console.log("그냥 값");
    const result = 10 + 10;
  };

  return (
    <div>
      <p>숫자 : {number}</p>
      <button onClick={increase}>increase</button>
      <button onClick={decrease}>decrease</button>

      <p>숫자에 10을 더한 값 : {plus10()}</p>
    </div>
  );
}
```

### useMemo 사용하기

- 생각보다 간단하게 바꿀 수 있다.
- 기존의 코드에서 `initialNum`은 바뀌지 않기 때문에 콘솔이 찍히지 않는다.
- `plus10`함수에 대한 리렌더링이 일어나지 않는다는 것을 의미한다.
- 삽질을 하느라 오래 걸렸지만 어떤 느낌으로 사용되지는 알 수 있었으며, 더 복잡한 예시를 작성해볼 필요성을 느꼈다.

```jsx
function App() {
  const [number, setNumber] = useState(0);
  const [initialNum, setinitialNum] = useState(0);

  const increase = () => {
    console.log("숫자가 증가합니다.");
    setNumber(number + 1);
  };

  const decrease = () => {
    console.log("숫자가 감소합니다.");
    setNumber(number - 1);
  };

  const plus10 = useMemo(() => {
    console.log("그냥 값");
    const result = initialNum + 10;
    return result;
  }, [initialNum]);

  //   const plus10 = () => {
  //     console.log("그냥 값")
  //     return initialNum + 10;
  //   }

  return (
    <div>
      <p>숫자 : {number}</p>
      <button onClick={increase}>increase</button>
      <button onClick={decrease}>decrease</button>

      <p>숫자에 10을 더한 값 : {plus10}</p>
    </div>
  );
}
```

## useCallback

- `useMemo`와 마찬가지로, 값이 아닌 함수를 메모이제이션 하기 위해서 사용된다.
- 첫 번째 인자로 함수를, 두 번째 인자로 배열을 넣어두면, 배열이 변경될 때까지 저장해놓고 재사용할 수 있다.
- 자바스크립트에서 함수도 객체로 취급되기 때문에 메모리 주소에 의한 참조 비교가 일어난다.
- 컴포넌트가 렌더링 될 때마다 함수를 새로 선언되는 것을 방지하여 성능 항샹을 목적으로 하는데, 단순히 컴포넌트 내에서 함수를 반복해서 생성하지 않기 위해 사용하는 것은 큰 의미가 없거나, `useCallback`조차도 메모리를 사용하기 때문에 오히려 손해인 경우도 있다.
- `useEffect`처럼 첫 번째 인자가 두 번째 인자를 의존하여, 두 번째 인자가 변경될 때만 첫 번째 인자의 코드가 실행되듯이, 첫 번째 함수가 의존해야 하는 배열인 두 번째 인자로 함수를 호출한다.

### 예시

- 아래의 코드에서 컴포넌트의 API 호출 코드는 `fetchUser` 함수가 변경될 때만 호출하려고 한다.
- `fetchUser`은 함수이기 때문에, `userId`가 바뀌든 말든 컴포넌트가 렌더링될 때마다 (함수가, 함수 또한 객체로 취급되기 때문에) 새로운 참조값으로 변경된다.
- 그러면 `useEffect()` 함수가 호출되어 `user`상태값이 바뀌고, 또 다시 `useEffect()`함수가 호출되는 불필요한 재호출이 발생된다.
  - 참조값이 다르면 `false`이기 때문에, 다르다고 판단된다.

```js
function Profile({ userId }) {
  const [user, setUser] = useState(null);

  const fetchUser = () =>
    fetch("https://your-api.com/users/${userId}")
      .then((res) => res.json())
      .then(({ user }) => user);

  useEffect(() => {
    fetchUser().then((user) => setUser(user));
  }, [fetchUser]);
}
```

- 이러한 상황에서 `useCallback` 함수를 이용하여 컴포넌트가 다시 렌더링되더라도 참조값을 동일하게 유지시킬 수 있다.
- `useCallback`을 사용하여 의도했던대로 `userId`가 바뀌었을 때만 `fetchUser` 함수가 실행되게 하고, `fetchUser`가 변경될 때만 `useEffect`가 실행되도록 한다.

```js
function Profile({ userId }) {
  const [user, setUser] = useState(null);

  const fetchUser = useCallback(
    () =>
      fetch("https://your-api.com/users/${userId}")
        .then((res) => res.json())
        .then(({ user }) => user),
    [userId]
  );
}

userEffect(() => {
  fetchUser().then((user) => setUser(user));
}, [fetchUser]);
```

#### React.memo()

- `useMemo, useCallback`과 마찬가지로 컴포넌트 자체를 기억하여, 다음 렌더링이 일어날 때 `props`가 같다면 기억된 컴포넌트를 재사용한다.
- 같은 `props`로 렌더링이 자주 일어나는 컴포넌트에 사용할 때 유용하다.
-

```jsx
export function Movie({ title, date }) {
  return (
    <div>
      <div>Movie Title: {title}</div>
      <div>Movie Date: {date}</div>
    </div>
  );
}

export const MemoMovie = React.memo(Movie);
```

### 활용예시

- 컴포넌트를 `React.memo`로, 함수를 `useCallback`으로 활용할 수 있다.

```jsx
function Light({ room, on, toggle }) {
  console.log({ room, on });
  return (
    <button onClick={toggle}>
      {room} {on ? "on" : "off"}
    </button>
  );
}

export default function SmartHome() {
  const [room1, setRoom1] = useState(false);
  const [room2, setRoom2] = useState(false);
  const [room3, setRoom3] = useState(false);

  const toggleRoom1 = () => setRoom1(!room1);
  const toggleRoom2 = () => setRoom2(!room2);
  const toggleRoom3 = () => setRoom3(!room3);

  return (
    <div>
      <Light room="침실" on={room1} toggle={toggleRoom1}></Light>
      <Light room="욕실" on={room2} toggle={toggleRoom2}></Light>
      <Light room="거실" on={room3} toggle={toggleRoom3}></Light>
    </div>
  );
}
```

- 위의 코드는 방의 불을 끄고 켤 때마다 `console`에 방에 대한 정보와 불이 켜지고 꺼진 것에 대한 정보를 나타낸다.
- `button`을 클릭하면 `console`에 침실, 욕실, 거실에 대한 메세지가 전부 출력되는데, 이는 `Light` 컴포넌트가 렌더링될 때마다 해당 방에 대한 메세지를 출력하게 되고, 버튼 클릭으로 인한 상태 변경과는 독립적으로 동작하기 때문이다.
- 내가 클릭한 것에 대한 컴포넌트가 리렌더링되길 원한다면 `React.memo`와 `useCallback`을 함께 활용할 수 있게 된다.
  - 우선 `Light`컴포넌트를 `React.memo`로 감싸, 전달되는 `props`가 변경되지 않는다면 리렌더링되지 않도록 한다.
  - 컴포넌트에 대한 불필요한 렌더링은 방지했지만, `SmartHome`에 대한 상태값이 변경되며 리렌더링이 발생하게 되고, 작성한 `toggleRoom`함수에 대한 새로운 참조값을 할당하게 되는 불필요한 렌더링을 방지하기 위해 `useCallback`을 함께 활용한다.

```jsx
function Light({ room, on, toggle }) {
  console.log({ room, on });
  return (
    <button onClick={toggle}>
      {room} {on ? "on" : "off"}
    </button>
  );
}

Light = React.memo();
// 컴포넌트의 불필요한 렌더링 방지

export default function SmartHome() {
  const [room1, setRoom1] = useState(false);
  const [room2, setRoom2] = useState(false);
  const [room3, setRoom3] = useState(false);

  const toggleRoom1 = useCallback(() => {
    setRoom1(!room1);
  }, [room1]);
  const toggleRoom2 = useCallback(() => {
    setRoom1(!room2);
  }, [room3]);
  const toggleRoom3 = useCallback(() => {
    setRoom1(!room3);
  }, [room3]);
  // 함수 리렌더링 방지

  // const toggleRoom1 = () => setRoom1(!room1);
  // const toggleRoom2 = () => setRoom2(!room2);
  // const toggleRoom3 = () => setRoom3(!room3);

  return (
    <div>
      <Light room="침실" on={room1} toggle={toggleRoom1}></Light>
      <Light room="욕실" on={room2} toggle={toggleRoom2}></Light>
      <Light room="거실" on={room3} toggle={toggleRoom3}></Light>
    </div>
  );
}
```
