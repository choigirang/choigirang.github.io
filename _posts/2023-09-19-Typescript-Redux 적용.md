---
layout: post
title: TypeScript 11장 - Redux 타입
author: admin
date: 2023-09-20 00:00:00 +900
lastmod: 2023-09-20 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [TYPESCRIPT]
tags: [typescript, javascript, react, redux]
---

# TypeScript

## Redux

- 상태관리를 하기 위해 redux를 적용할 시 작성해야 하는 타입스크립트 적용법이다.

### actions

- 액션 타입을 선언함에 있어, `as const`를 붙여주어 일반 문자열이 아닌 `counter/INCREASE`로 추론될 수 있도록 해준다.
- 액션 타입을 일반적은 문자열로 추론해버릴 경우, 액션 객체들에 대한 타입 지정에서 타입 에러가 발생한다.

```js
// actions
const INCREASE = 'counter/INCREASE' as const;
const DECREASE = 'counter/DECREASE' as const;
const INCREASE_BY = 'counter/INCREASE_BY' as const;

export const increase = () => ({
    type: INCREASE,
});

export const decrease = () => ({
    type: DECREASE,
});

export const increaseBy = (diff: number) => ({
  type: INCREASE_BY,
  payload: diff
});
```

- `ReturnType`은 특정 함수의 반환값을 추론해준다.
- 이 부분에서 `CounterAction`의 타입을 문자열이 아닌 우리가 사용할 액션 타입인 `counter/...`로 정확하게 추론해주어야 에러가 발생하지 않는다.

```js
// types
export type CounterAction =
  | ReturnType<typeof increase>
  | ReturnType<typeof decrease>
  | ReturnType<typeof increaseBy>;
```

### reducer

- 리듀서는 어떠한 액션이 발생했을 때 어떠한 동작을 해주는 지에 대한 세부 항목이라고 생각하면 된다.
- number 라는 actions이 있을 때, 이 actions 중 타입이 "`A`일 때는 어떠한 동작을 해주고 `B`일 때는 어떠한 동작을 해줘" 와 같다.

```js
type initialData = {
  count: number,
};

const initial: initialData = {
  count: 0,
};

function counter(state: initialData = inital, action: CounterAction) {
  switch (action.type) {
    case INCREASE:
      return { count: state.count + 1 };
    case DECREASE:
      return { count: state.count - 1 };
    case INCREASE_BY:
      return { count: state.count + action.payload };
    default:
      return state;
  }
}

export default counter;
```

### store

- 전역적으로 상태 저장 역할을 할 store을 만든다.
- `combineReducers`로 작성한 reducers들을 통합해준다.

```js
// store
const rootReducer = combineReducers({
  counter,
});

export const store = createStore(rootReducer);

export type RootState = ReturnType<typeof rootRedcuer>;
// 나중에 reducer를 불러와 사용할 때 state에 대한 타입을 지정해주어야 한다.
```

### 활용

- 위에서 만든 reducer를 어떻게 활용하는지 알아보도록 하자.
- useSelector를 이용해 우리가 작성한 reducer 중 counter 함수를 지칭한다.
- counter 함수의 초깃값으로는 `count : 0` 이라는 값을 불러온다.

```jsx
function CounterContainer() {
  const count = useSelector((state: RootState) => state.counter.count);

  const dispatch = useDispatch();

  const onIncrease = () => {
    dispatch(increase());
  };

  const onDecrease = () => {
    dispatch(decrease());
  };

  const onIncreaseBy = (diff: number) => {
    dispatch(increaseBy(diff));
  };

  return (
    <Counter
      count={count}
      onIncrease={onIncrease}
      onDecrease={onDecrease}
      onIncreaseBy={onIncreaseBy}
    />
  );
}
```
