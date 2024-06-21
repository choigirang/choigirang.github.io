---
layout: post
title: React 73장 - React-Query hooks 초깃값 설정하기
author: admin
date: 2024-06-01 00:00:00 +900
lastmod: 2024-06-01  00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, query, hooks]
---

# React

## React-Query

### query hook의 최초 실행

- 리액트 쿼리를 사용하다 적어보는 포스터
- 리액트 쿼리를 hook으로 만들어 사용하는 것은, 반복되는 로직을 줄일 수 있고 코드를 관리하기 편하다는 장점이 있다.
- 이전 프로젝트의 경우, 선택된 `stack`이라는 데이터 값을 쿼리 훅에 넘겨주고, 전달받은 데이터를 API요청에 함께 사용한다.
- 위와 같은 방식으로 훅을 사용할 경우 에러가 발생하는 것을 볼 수 있는데, 초기에는 선택된 `stack` 데이터가 없기 때문이다.
- 선택된 데이터는 없지만 `쿼리 훅은 컴포넌트가 렌더링 될 때 최초에 한 번 실행되기 때문에 선택되지 않는 값으로 API요청을 할 것이고, 에러를 반환할 것이다.`

```jsx
async function getData(stack: string){
  const res = await fetch(`.../data/${stack}`)
  return res
}

export default function useQueryHook(stack: string){
  const {data} = useQuery<AxiosResponse<ExampleData[]>>(["exampe", stack], () => getData(), {enable: false})

  return {data}
}
```

- 위와 같은 형태로 작성된 쿼리 훅에 옵션값으로 `{enable: false}`를 작성하면 쿼리 훅의 최초의 실행을 방지할 수 있다

### 특정 조건에 의한 실행

- 다른 경우로, 위처럼 파라미터를 전달하여 쿼리 훅을 실행시키고 있을 때, 파라미터가 존재할 경우에만 실행할 수 있도록 `enable` 옵션을 설정할 수 있다.

```jsx
export default function useQueryHook(stack:string){
  // 동일
  ...({enable: !!stack })
}
```

### useEffect || refetch

- `useEffect`와 `refetch`를 이용할 수도 있다.
- 쿼리 훅을 사용하는 컴포넌트나 쿼리 훅 내에서 파라미터값을 넣어 `refetch`를 실행시킨다.

```jsx
// query hook
// 동일 fetch func
export default function useQueryHook(stack:string){

  useEffct(() => {
    if(stack){
      refetch()
    }
  },[stack])

  const {data} = useQuery<AxiosResponse<ExampleData[]>>(["example", stack], () => getData(stack), {enable: false})
  return {data}
}
```
