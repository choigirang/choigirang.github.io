---
layout: post
title: React 44장 - React-Query (2)
author: admin
date: 2023-04-22 00:00:00 +900
lastmod: 2023-04-22 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, usequery, query, mutate, usemutation]
---

# React

## React Query

#### 1. Pagenation

- 이전 글에서 작동했던 것처럼 의존성 배열을 추가하여, 데이터를 가진 페이지의 번호가 바뀔 때마다 새로운 데이터들을 불러오도록 지정하여 button에 대한 이벤트를 처리할 수 있다.

```jsx
async function fetchPosts() {
  const response = await fetch("http://example.com/posts?limit=10&page=1");
  return response.json();
}

function Post() {
  const [pageNum, setPageNum] = useState(0);

  const { data, isLoading, isError, error } = useQuery("post", fetchPosts);

  return (
    <>
      ...중략
      <button>Previous Page</button>
      <button>Next Page</button>
    </>
  );
}
```

- 기존에 작성된 코드에서 의존성 배열을 추가해주고, `fetchPosts`에 pageNum에 관한 정보를 넘겨주어 pageNum에 해당하는 페이지에 들어있는 데이터들을 받아오도록 한다.
- post에 관한 각 댓글의 목록을 받아오는 방식과 똑같다.

```jsx
function Post() {
  const [pageNum, setPageNum] = useState(1);
  const maxPage = 10;

  const { data, isLoading, isError, error } = useQuery(["post", pageNum], () =>
    fetchPosts(pageNum)
  );

  return (
    <>
      ...중략
      <button disable={pageNum <= 0} onClick={() => setPageNum(pageNum - 1)}>
        Previous Page
      </button>
      <button disable={pageNum >= 10} onClick={() => setPageNum(pageNum + 1)}>
        Next Page
      </button>
    </>
  );
}

async function fetchPosts(pageNum) {
  const response = await fetch(
    `http://example.com/posts?limit=10&page=${pageNum}`
  );
  return response.json();
}
```

#### 2.Prefetching

- App 컴포넌트에서 선언한 queryClient의 메서드인 prfetch 쿼리를 가져온다.
- 가져온 훅을 기능 컴포넌트에서 선언 후 사용한다.
- isLoading 을 사용하여 page가 바뀔 때마다 매번 새로운 데이터를 요청하고 이를 기다리는 과정을 반복하면 사용자 경험에 좋지 않은 영향을 끼친다.
- 따라서 page가 바뀔 때마다 다음 페이지에 대한 데이터를 미리 받아오는 작업을 거치고, 이 데이터를 캐시해놓아 사용자가 다음 page를 눌렀을 떄 캐시된 데이터를 미리 보여주는 방식을 사용한다.

```jsx
import { useQuery, useQueryClient } from "react-query";

function Posts() {
  const queryClient = useQueryClient();
  useEffect(() => {
    if(pageNUm < maxPage>){
      const nextPage = pageNum + 1;
      queryClient.prefetchQuery(["posts", nextPage], () => fetch(nextPage));
    }
  }, [pageNum, queryClient]);
}
```

- pageNum이 바뀔 때마다 nexPage에 대한 데이터를 미리 가져온다.
- `prefetch`로 사용할 key 값은 우리가 이전에 사용한 fetch의 키와 동일하다.
- 우리가 이전 페이지로 돌아갔을 때도 데이터를 유지하도록 설정을 추가한다.

```jsx
const { data, isLoading, isError, error } = useQuery(
  ["posts", pageNum],
  () => fetchPosts(pageNum),
  {
    staleTime: 2000,
    keepPreviousData: true,
  }
);
```

#### 3. isFetching

- isLoading과 마찬가지로 isFetching 또한 사용할 수 있다.
- isFetching은 isLoading을 포함하고 있으며, isLoading은 쿼리에 대해 캐시된 데이터가 없는 상태를 뜻한다.
- 위에서 prefetch를 사용하여 미리 데이터를 받아왔다고 하더라도 캐시된 존재 여부에 관계 없이 prefetch 이전에 실행되기 떄문에 isFetching을 사용할 경우 항상 참이 된다.

#### 4. Mutations

- useMutation을 사용하여 데이터를 삭제, 추가 등의 역할을 한다.
- 쿼리를 관리하며 캐시를 업데이트하고 자동으로 리렌더링을 처리한다.
- useQuery와 마찬가지로 몇 가지 인자를 받는다.
- mutate는 실행될 비동기 함수로 promise를 반환하며 api에 요청을 하는 함수이다.
  - 아래의 코드에서는 `mutate`로 addTodo를 실행시킨다.
- isLoading, isError, isSuccess를 사용하여 데이터를 기다리고 요청이 성공적으로 수행됐을 때의 동작들을 해결한다.

```jsx
const addTodo = async (newTodo) => {
  const { data } = await axios.post("/todos", newTodo);
  return data;
};

const { mutate, isLoading, isError, error, isSuccess } = useMutation(addTodo);

return (
  <>
    {isLoading && <div>isLoading...</div>}
    {isError && <div>Error...</div>}
    {isSuccess && <div>Add data...</div>}
    <button onClick={mutate({ id: 1, todo: "add todo" })}>추가하기</button>
  </>
);
```

#### 4.1 Test

- isLoading, isError, error는 `useQuery`에서 사용 중이기 때문에 또 다시 구조분해할당을 할 필요가 없다.
- `useMutation`은 변경된 데이터를 캐시할 필요가 없기 때문에 쿼리 키가 필요하지 않다.
- 내장 함수인 `mutate`를 사용해 `useMutation`을 실행시킨다.

```jsx
async function fetchComments(postId) {
  const res = await axios.get(`http://localhost:3000/post/${postId}`);
  return res.json();
}

async function deletePost(postId) {
  const res = await axios.delete(`http://localhost:3000/post/${postId}`);
  return res.json();
}

async function updatePost(postId) {
  const res = await axios.post(`http://localhost:3000/post/${postId}`);
  return res.json();
}

function PostDetail({ post }) {
  const { data, isLoading, isError, error } = useQuery("showComments", () =>
    fetchComments(post.id)
  );
  const deleteData = useMutation((postId) => deletePost(postId));
  const updateData = useMutation((postId) => updatePost(postId));

  return (
    <>
      <button onClick={deleteData.mutate(post.id)}>delete</button>
      <button onClick={updateData.mutate(post.id)}>update</button>
      {deleteData.isLoading && <div>delete loading...</div>}
      {deleteData.isError && <div>delete error...</div>}
      {updateData.isLoading && <div>update loading...</div>}
      {updateData.isError && <div>update error...</div>}
    </>
  );
}
```

#### 정리

- QueryClient, QueryProvider
  - 최상위 컴포넌트에서 컴포넌트들을 감싸, 하위 컴포넌트에서 query를 사용할 수 있도록 한다.
- useQuery
  - isLoading,isError 등으로 사용자에게 상태를 제공한다.
  - 매번 isLoading,isError가 실행되면 사용자 경험에 악영향을 끼치기 때문에 isFetching 활용한다.
- staleTime, cacheTime
  - 데이터가 만료되어 새롭게 데이터를 가져올 주기와 데이터가 만료되어 삭제될 주기를 정한다.
- useMutation
  - 데이터의 변이를 말하며 데이터를 전송하거나 삭제하는 동작을 한다.
