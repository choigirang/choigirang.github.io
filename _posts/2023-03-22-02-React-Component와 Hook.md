---
layout: post
title: React 35장 - hook
author: admin
date: 2023-03-22 00:00:00 +900
lastmod: 2023-03-22 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, hook]
---

# React

## Component와 Hook

### Function Component와 Class Component

- Hook은 함수 컴포넌트에서 사용하는 메서드이다.
- 함수 컴포넌트 이전에는 클래스 컴포넌트가 있었다.
- 클래스 컴포넌트는 복잡해질수록 이해하기 어려워졌고, 컴포넌트 사이에서 상태 로직을 재사용하기 어렵다는 단점이 있었다.
- 리액트의 클래스 컴포넌트를 사용하기 위해서는 JavaScript의 this 키워드가 어떤 방식으로 동작하는지 알아야 하는데, 문법을 정확히 알지 못하면 동작 방식 자체를 정확히 이해하기 어렵다.
- 따라서 클래스 컴포넌트에서 함수 컴포넌트로 넘어갔으며, 함수 컴포넌트는 클래스 컴포넌트와는 다르게 상태 값을 사용하거나 최적화할 수 있는 기능들이 미진했고 이를 보완하기 위해 Hook 개념을 도입했다.

#### Class Component

```jsx
class Counter extends Component {
  constructor(props) {
    super(props);
    this.state = {
      counter: 0,
    };
    this.handleIncrease = this.handleIncrease.bind(this);
  }

  handleIncrease = () => {
    this.setState({
      counter: this.state.counter + 1,
    });
  };

  render() {
    return (
      <div>
        <p>You clicked {this.state.counter} times</p>
        <button onClick={this.handleIncrease}>Click me</button>
      </div>
    );
  }
}
```

#### Function Component

- 함수형 컴포넌트는 클래스형 컴포넌트에 비해 훨씬 더 직관적이고 보기 쉽다는 특징이 있다.
- 컴포넌트에서 숫자를 올리기 위해 상태값을 저장하고 사용할 수 있게 해주는 `useState()`가 대표적인 Hook이다.

```jsx
function Counter() {
  const [counter, setCounter] = useState(0);

  const handleIncrease = () => {
    setCounter(counter + 1);
  };

  return (
    <div>
      <p>You clicked {counter} times</p>
      <button onClick={handleIncrease}>Click me</button>
    </div>
  );
}
```

## Hook이란 ?

- 리액트 16.8에 새로 추가된 기능으, class를 작성하지 않고도 state와 다른 React의 기능들을 사용할 수 있게 해준다.
- 함수형 컴포넌트에서 상태값 및 다른 여러 기능을 사용하기 편리하게 해주는 메서드를 의미한다.
- Hook은 class가 아닌 function으로만 리액트를 사용할 수 있게 해주는 것이기 때문에 클래스형 컴포넌트에서는 동작하지 않는다.

### Hook 사용 규칙

1. 리액트 함수의 최상위에서만 호출해야 한다.

   1. 반복문,조건문,중첩된 함수 내에서 Hook을 실행하면 예상한 대로 동작하지 않을 우려가 있다.

   ```jsx
   if (counter) {
     const [sample, setSample] = useState(0);
   }
   ```

2. 오직 리액트 함수 내에서만 사용되어야 한다.
   ```jsx
   window.onload = function () {
     useEffect(() => {
       // do something...
     }, [counter]);
   };
   ```

### useMemo

- 특정값을 재사용하고자 할 때 사용하는 hook이다.
- 아래의 예시는 props로 넘어온 `value` 값을 `calculate`라는 함수에 인자로 넘겨서 `result` 값을 구한 후, `<div>` 엘리먼트로 출력을 하고 있다.

  ```jsx
  function Calculator({ value }) {
    const result = calculate(value);

    return (
      <>
        <div>{result}</div>
      </>
    );
  }
  ```

- 만약 `calculate`가 내부적으로 복잡한 연산을 해야 하는 함수라 계산된 값을 반환하는 데에 시간이 몇 초 이상 걸린다고 가정한다면, 해당 컴포넌트는 렌더링 할 때마다 이 함수를 계속해서 호출할 것이고, 그 때마다 몇 초 이상 소요가 될 것이다.
- 값이 계속 바뀌는 경우가 아니라면, 값을 어딘가에 저장 해뒀다가 다시 꺼내 쓸 수 있다면 굳이 `calculate` 함수를 호출할 필요가 없으며 `useMemo`를 호출하여 `calculate`를 깜싸주면 이전에 구축된 렌더링과 새로이 구축되는 렌더링을 비교해 `value`값이 동일할 경우 이전 렌더링의 `value`값을 그대로 재활용할 수 있게 된다.

  ```jsx
  import { useMemo } from "react";

  function Calculator({ value }) {
    const result = useMemo(() => calculate(value), [value]);

    return (
      <>
        <div>{result}</div>
      </>
    );
  }
  ```

## Memoization

- 기존에 수행한 연산의 결과값을 메모리에 저장 해두고, 동일한 입력이 들어오면 재활용하는 프로그래밍 기법을 말한다.
- 메모이제이션을 적절히 사용한다면 굳이 중복 연산을 할 필요가 없기 때문에 앱의 성능을 최적화할 수 있다.
- `useMemo`는 복잡한 연산의 중복을 피하고 리액트 앱의 성능을 최적화시킨다.

## 실습

[useMemo를 활용하여 최적화시키기](https://codesandbox.io/s/usememo-silseub-0xgqju?from-embed)

- 위 코드에서 실제로 연산 로직에 영향을 주는 값은 `val1`과 `val2`이다.
- 이름 상태가 변화하면 `add` 함수가 게속 같은 결괏값을 리턴함에도 불구하고 불필요하게 계속 호출되고 있기 때문에, `useMemo`를 이용하여 `add`함수의 호출을 최소화한다.

## useCallback

- `useMemo`와 마찬가지로 메모이제이션 기법을 이용한 Hook이다.
- `useMemo`는 값의 재사용을, `useCallback`은 함수의 재사용을 위해 사용하는 Hook이다.
- `Calculator` 컴포넌트 내에 있는 `add`라는 함수는 props로 넘어온 `x`와 `y` 값을 더해 `<div>` 태그에 값을 출력하고 있다.
- `useMemo`와 마찬가지로 해당 컴포넌트가 리렌더링 되더라도 그 함수가 의존하고 있는 값인 `x`와 `y`가 바뀌지 않는다면 함수 또한 메모리 어딘가에 저장해 뒀다가 다시 꺼내서 쓸 수 있을 것이다.

  ```jsx
  function Calculate({ x, y }) {
    const add = () => x + y;

    return (
      <>
        <div>{add()}</div>
      </>
    );
  }
  ```

- `useCallback` Hook을 사용하면 그 함수가 의존하는 값들이 바뀌지 않는 한 기존 함수를 게속해서 반환한다.
- `x`와 `y` 값이 동일하다면 다음 렌더링 때 이 함수를 다시 사용한다.
- `useCallback`만 사용해서는 `useMemo`에 비해 큰 차이의 최적화를 느낄 수 없다.
- `useCallback`은 함수를 호출하지 않는 Hook이 아니라, 그저 메모리 어딘가에 함수를 꺼내서 호출하는 Hook이기 때문이다.
- 단순히 컴포넌트 내에서 함수를 반복해서 생성하지 않기 위해서 `useCallback`을 사용하는 것은 큰 의미가 없거나 오히려 손해인 경우도 있다.
- 따라서 자식 컴포넌트의 props로 함수를 전달해줄 때 `useCallback`을 사용하기 좋다.

  ```jsx
  import React,{useCallbacks} from 'react

  function Calculator({x,y}){
    const add = useCallback(()=> x+y, [x,y]);

    return <>
        <div>
            {add()}
        </div>
    </>;
  }
  ```

### useCallback과 참조 동등성

- `useCallback`은 참조 동등성에 의존한다.
- 리액트는 JavaScript 언어로 만들어진 오픈소스 라이브러리이기 때문에 기본적으로 JavaScript 문법을 따라간다.
- JavaScript에서 함수는 객체이며, 객체는 메모리에 저장할 때 값을 저장하는 게 아니라 값의 주소를 저장하기 때문에, 반환하는 값이 같을 지라도 일치연산자로 비교했을 때 `false`가 출력된다.
- 같은 함수를 할당했음에도 메모리 주소 값이 다르기 때문에 같다고 보지 않는다.
- JavaScript에서 함수는 개체이며 두 개의 함수는 동일한 코드를 공유하더라도 메모리 주소가 다르기 때문에, 메모리 주소에 의한 참조 비교 시 다른 함수로 본다.
- 리액트 또한 리렌더링 시 함수를 새로이 만들어서 호출하며, 새로이 만든 함수는 기존의 함수와 같은 함수가 아니다.
- `useCallback`을 이용해 함수 자체를 저장해서 다시 사용하면 함수의 메모리 주소 값을 저장했다가 다시 사용한다는 것과 같다고 볼 수 있다.
- 따라서 리액트 컴포넌트 함수 내에서 다른 함수의 인자로 넘기거나 자식 컴포넌트의 prop로 넘길 때 예상치 못한 성능 문제를 막을 수 있다.

  ```jsx
  function doubleFactory() {
    return (a) => 2 * a;
  }

  const double1 = doubleFactory();
  const double2 = doubleFactory();

  double1(8); // 16
  double2(8); // 16

  double1 === double2; // false
  double1 === double1; // true
  ```

## 실습

[useCallback으로 최적화하기](https://codesandbox.io/s/usecallback-silseub-4bfhpi?from-embed)

- `input`창에 숫자를 입력해 콘솔에 "아이템을 가져옵니다."가 출력된다.
- `button`을 눌러도 콘솔에 출력되는데, 버튼을 누를 때도 앱이 리렌더링 되므로, App 내부의 `getItems()`함수가 다시 만들어진다.
- 새로이 만들어진 함수는 이전의 함수와 참조 비교시 다른 함수이기 때문에 List 구성 요소 내에서 `useEffect` Hook은 `setItems`를 호출하고 종속성이 변경됨에 따라 콘솔이 출력된다.
