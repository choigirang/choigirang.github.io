---
layout: post
title: React 63장 - react-query 다시 배우기 (3) query key 관리하기
author: admin
date: 2023-10-26 00:00:00 +900
lastmod: 2023-10-30  00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, query, key]
---

# React

[리액트 쿼리 다시 배우기 (2)](<https://choigirang.github.io/posts/02-React-Query-%EB%8B%A4%EC%8B%9C%EB%B0%B0%EC%9A%B0%EA%B8%B0(2)/>)</br>
[참고자료 1](https://maxkim-j.github.io/posts/react-query-preview/)</br>
[참고자료 2](<https://heycoding.tistory.com/128#3.%20invalidateQueries()%EB%A7%90%EA%B3%A0%20setQueryData()%20%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0>)</br>

## React-Query

- 미니 프로젝트를 만들어보다가 `useMutation`을 사용하며 `queryClient.invalidateQueries`을 통해 쿼리 키를 초기화시켜, `mutate`를 통해 글을 작성했을 때 내가 작성한 데이터를 자동으로 받아오려고 했다.
- 문제가 된 부분, 하나의 `useFetch`를 사용하고 있기 때문에 처음에 모든 데이터를 보여줄 때에는 쿼리 키를 `all`로 지정해줬다.
- 그리고 내가 클릭한 카테고리에 대한 데이터를 받아올 때는 쿼리 키를 카테고리로 지정해줬다.
- 여기서 문제점은 `invalidateQueries`로 쿼리 키를 초기화 해주더라도 개별 데이터를 관리하는 것이 잘못됐다.
- 그럼 미리 생성된 쿼리 키를 확인하여 초기화해주는 것은 어떨까? 라는 생각에 쿼리의 속성을 들여다보았다.

```tsx
// example
function Example(category: { category?: string }) {
  const posts = useQuery<AxiosResponse<ApiStackData[]>>(
    [stack !== "" ? stack : ""],
    () => api.get(`/posts/${stack !== "" ? stack : "all"}`)
  );

  return { posts };
}

// 쿼리 키를 확인하기
const findQueryKey = queryClient.getQueryCache()
// getQueryCache()
QueryCache
{
    config: {},
    listeners: [f],
    queries: [Query, Query], // 생성된 쿼리 데이터
    queriesMap: {[""]:Query,  ["example"]:Query}, // 쿼리 키에 해당하는 쿼리 데이터
    [[Prototype]]
}
```

- 위와 같은 쿼리의 메서드 중에서 생성된 쿼리 키를 찾기 위해 `queryClient.getQueryCache()["quriesMap"]`을 접근해보려고 했지만, 너무 복잡한 과정 때문에 생성된 쿼리 키를 찾기 보다는 처음 요청때 사용하는 쿼리 키를 잘 관리해봐야겠다는 필요성을 느끼게 되었다.

### 쿼리 키

- 쿼리 키에 대해 알아보자.
- 리액트 쿼리는 내부적으로 데이터를 캐시하고 쿼리에 대한 종속성이 변경될 때 자동으로 데이터를 다시 가져온다.
- 이 때 쿼리 키를 사용하여 쿼리 캐시와 상호작용한다.

```jsx
useQuery(["example", {"example1", "example2", "example3"}], ...);
useQuery(["example", {"example2", "example1", "example3"}], ...);
useQuery(["example", {"example2", "example3", "example1"}], ...);
```

```jsx
useQuery(["example", "example1", "example2", "example3"], ...);
useQuery(["example", "example1", "example3", "example2"], ...);
useQuery(["example", "example2", "example1", "example3"], ...);
```

- 첫 번째와 두 번째 예시를 살펴보면, 첫 번째의 쿼리 키는 모두 같은 취급을 받기 때문에 동일한 쿼리 키이고, 두 번째의 쿼리 키는 배열의 순서가 달리되기 때문에 각 각의 쿼리 키로 취급된다.

#### 쿼리 키 관리하기

- 쿼리 키는 항상 배열로 관리하는 것이 좋다.
- 리액트 쿼리는 내부적으로 키를 배열로 반환하기 때문에 배열을 사용해서 관리하는 것과 같지만, 코드 컨벤션을 맞추기 위해 배열을 사용하도록 하자.

```jsx
// 같은 쿼리 키가 된다.
useQuery("example") = useQuery(["example"])
```

##### invalidateQueries(exact, predicate)

- 쿼리 키에 해당하는 데이터를 새로운 데이터로 교체할 수 있도록, 쿼리 데이터를 오래된 데이터로 취급하여 새로운 데이터로 업데이트 한다.
- 새로운 데이터로 업데이트 한다는 것은 `refetch`와 동일하게 볼 수 있지만, `enabled` 옵션과 상관없이 무조건적으로 실행되는 `refetch`보다 더욱 스마트하게 사용할 수 있다.
-

```jsx
const removeQueryKeyDataOfKey = useMutation(updateFn, {
  onSuccess:
    // 모든 키를 무효화
    queryClient.invalidateQueries();

    // key로 시작하는 모든 쿼리를 무효화
    queryClient.invalidateQueries({queryKey: ["key"]})
} )

// removeQueryKey1과 2 모두 무효화
const removeQueryKey1 = useQuery(["key", 1], ...);
const removeQueryKey2 = useQuery(["key", {page: 1}], ...);
```

- 더욱 구체적인 키를 무효화시킬 수도 있다.

```jsx

const removeQueryKeyDataOfExact = useMutation(updateFn, {
  onSuccess:
    queryClient.invalidateQueries(["key", {page: 1}], ...);
})

const removeQueryKey = useQuery(["key", {page: 1}, {number: 1}], ...);
const notRemoveQueryKey = useQuery(["key", {page: 2}, {number: 2}], ...)
```

- **exact** 옵션을 이용할 수도 있다.

```jsx
// "key"로 시작하는 쿼리 키가 아닌 "key"에만 해당하는 데이터를 특정한다.
useMutation(updateFn, {
  onSuccess: queryClient.invalidateQueries(["key"]),
  exact: true,
});
```

- **predicate**로 `boolean`값을 정해 초기화시킬 수도 있다.

```jsx
useMutation(updateFn, {
  onSuccess: queryClient.invalidateQueries({
    predicate: (query) =>
      query.queryKey[0] === "key" &&
      query.queryKey[1].page &&
      query.queryKey[2]?.number >= 2,
  }),
});

const removeQueryKey = useQuery(["key", { page: 1 }, { number: 1 }]);
const notRemoveQueryKey = useQuery(["key", { number: 1 }, { page: 1 }]);
```

##### setQueryData, setQueriesData

- `invaidateQuries`를 통해 쿼리를 무효화시킨 후 데이터를 다시 가져올 수 있다.
- 하지만 서버에서 내려주는 데이터가 갱신된 새로운 데이터라면 새로운 `GET`요청없이 업데이트 할 수 있다.
- `setQueryData`는 캐시된 데이터를 즉시 업데이트하는 동기식 함수이며, 첫 번째 인자로는 업데이트시키고자 하는 쿼리 키를 입력하고, 두 번째 인자로는 업데이트 함수를 입력한다.
- 업데이트 함수의 인자에는 과거 데이터가 들어가는데, 이 값은 쿼리 키에 해당하는 기존에 가지고 있던 데이터를 의미하며, 서버에서 응답받은 데이터로 쿼리에 캐시된 데이터를 업데이트 할 수 있다.
- `oldData`는 불변성을 지켜야 한다.
- **데이터를 캐시에 직접 집어넣으면, 데이터가 서버에서 반환된 것처럼 동작하기 때문에 해당 쿼리를 사용하는 모든 컴포넌트가 리렌더링 되게 된다.**

```jsx
useMutation(updateFn, {
  onSuccess: (newData) => {
    queryClient.setQureyData([queryKey, newData.ex], (oldData) => {
      return { ...oldData, ...newData };
    });

    // 쿼리 키를 포함하는 모든 데이터를 업데이트
    queryClient.setQueriesData([queryKey, newData.ex], (oldData) => {
      return oldData.map((data) => (data.ex === newData.ex ? newData : data));
    });
  },
});
```
