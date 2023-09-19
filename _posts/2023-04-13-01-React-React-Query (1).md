---
layout: post
title: React 43장 - React-Query (1)
author: admin
date: 2023-04-21 00:00:00 +900
lastmod: 2023-04-21 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, usequery, query]
---

# React

## React Query

### 설치하기

- 서버에서 받아온 데이터의 캐시와 업데이트를 쉽게 관리할 수 있다.
- 리액트 쿼리를 설치한다.

```node
npm i react-query
npm i @types/react-query
// 타입스크립트 리액트 쿼리
```

### 사용법

#### 1. QueryClientProvider

- 리액트 쿼리를 설치했으면 쿼리를 사용할 최상위 컴포넌트를 감싸줘야한다.

```jsx
//App.jsx

import { QueryClient, QueryClientProvider } from "react-query";

const queryClient = new QueryClient();

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <div className="App"></div>
    </QueryClientProvider>
  );
}
```

#### 2. useQuery

- 이제 자식 컴포넌트에서 리액트 쿼리를 사용할 수 있다.
- 쿼리는 구조분해할당으로 받아올 데이터를 선언해주고, 몇 가지 인자가 온다.
  - 첫 번째는 쿼리를 사용할 `key`
  - 두 번째는 쿼리 함수를 사용한다. (데이터를 가져올 비동기 함수)

```jsx
import { useQuery } from "react-query";

async function fetchPosts() {
  const res = await fetch("http://example.com/posts");

  return res.json();
}

function Ex() {
  const { data } = useQuery("post", fetchPosts);

  return <div></div>;
}
```

- useQuery에는 다양한 내장 함수가 있다.
- `isLoading`은 데이터를 불러오기 전에 말 그대로 로딩 상태를 말한다.

  - `isLoading` 중인 경우 조기 리턴하는 식으로 사용할 수 있다.

    ```jsx
    const { data, isLoading } = useQuery("http://localhost:8080/", takeData);
    if (isLoading) {
      return <div>로딩 중 입니다.</div>;
    }
    ```

- `isError` 요청을 실패 했을 때를 말한다.
- `error` 자체를 불러올 수도 있다.

  ```jsx
  const { data, isError, error } = useQuery("http://localhost:8080", takeData);
  if (isError) {
    return (
      <>
        <div>데이터 요청을 실패했습니다.</div>
        <div>{error.toString()}</div>
      </>
    );
  }
  ```

#### 2-1. refetch

- `useQuery`는 브라우저의 포커싱이 변경될 때마다 새롭게 refetch시킨다.
- 이를 막기 위해서는 refetch에 대한 설정을 `false`로 변경해준다.

```jsx
const { data, isLoading, isErorr, error } = useQuery(
  "http://localhost:8080",
  fetchData,
  { refetchOnWindowFocus: false }
);
```

#### 3. QueryDevtools

- 최상위 컴포넌트에서 Query Devtools을 이용하여 데이터를 직관적으로 확인할 수 있다.
- `import { ReactQueryDevtools } from "react-query/devtools";` 최상위 컴포넌트에서 devtools를 import 해오고 컴포넌트를 불러온다.
- 브라우저에 쿼리 표시 마크가 새겨진 것을 확인할 수 있고, 이 Devtools를 이용해 데이터의 흐름을 확인할 수 있다.
  ![image](https://user-images.githubusercontent.com/118104644/233625571-6a5a1fdb-2052-4dcf-8e7f-299abbac6fa7.png){:.center}

```jsx
import { QueryClient, QueryClientProvider } from "react-query";
import { ReactQueryDevtools } from "react-query/devtools";

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <ReactQueryDevtools />
    </QueryClientProvider>
  );
}
```

#### 3-1. staleTime vs cacheTime

- `staleTime`은 만료된 데이터를 의미한다.
- 데이터가 만료되기까지의 시간 설정을 의미하며, 설정한 시간 동안 새로운 데이터를 가져오는 것이 아닌 만료되기 전의 데이터를 계속 사용하기 때문에 네트워크 요청이 줄어들고 애플리케이션의 성능을 개선할 수 있다.
- `useQuery`에 3번째 인자로 넣어주며 이는 option값이기 때문에 없어도 된다.
- 기본값은 0으로 `infinity` 또한 설정할 수 있다.
- `refetchOnWindowFocus`와 함께 설정도 가능하다.
- 새로운 데이터를 가져오는 주기를 결정하는 것과 관련이 있다.

```jsx
const { data, isLoading, isError, error } = useQuery("example", fetchData, {
  staleTime: 2000,
  refetchOnWindowFocus: false,
});
```

- `cacheTime`은 캐시된 데이터가 얼마나 오래 유지될 것인지 결정한다.
- 오래된 데이터가 자동으로 삭제되므로, 캐시를 유지하는 데이터의 양을 줄일 수 이고, 데이터의 수명을 결정하는 것과 관련이 있다.
- 두 옵션을 함께 사용하는 것을 권장하며, 최신 데이터를 보여주면서도 불필요한 캐시를 삭제하며 줄일 수 있다.

#### 4. Test

- Post 컴포넌트에서 api 요청으로 블로그 글을 받아오고, 받아온 포스팅 목록의 타이틀을 클릭했을 때, 클릭한 요소의 작성자와 댓글을 가져오는 코드를 작성한다.

```jsx
async function fetchPosts() {
  const response = await fetch(
    "https://jsonplaceholder.typicode.com/posts?_limit=10&_page=0"
  );
  return response.json();
}

function Post() {
  const [selectedPost, setSeletedPost] = useState(null);

  const { data, isLoading, isError, error } = useQuery("post", fetchPosts);

  if (isLoading) {
    return (
      <>
        <div>is Loading...</div>
      </>
    );
  }

  if (isError) {
    return (
      <>
        <div>
          Error...
          {error.toString()}
        </div>
      </>
    );
  }

  return (
    <>
      <ul>
        {data.map((post) => (
          <li
            key={post.id}
            className="post-title"
            onClick={() => setSelectedPost(post)}
          >
            {post.title}
          </li>
        ))}
      </ul>
      {selectedPost && <PostDetail post={selectedPost} />}
    </>
  );

  async function fetchComments(postId) {
    const response = await fetch(
      `https://jsonplaceholder.typicode.com/comments?postId=${postId}`
    );
    return response.json();
  }

  function PostDetail({ post }) {
    const { data, isLoading, isError, error } = useQuery("comment", () =>
      fetchComments(post.id)
    );
    // 일치하는 postId의 댓글을 가져오기 위해 함수를 바로 실행시킬 수 있다.

    if (isLoading) {
      return (
        <>
          <div>is Loading...</div>
        </>
      );
    }

    if (isError) {
      return (
        <>
          <div>
            Error...
            {error.toString()}
          </div>
        </>
      );
    }
  }

  return (
    <>
      <h3 style={{ color: "blue" }}>{post.title}</h3>
      <button>Delete</button> <button>Update title</button>
      <p>{post.body}</p>
      <h4>Comments</h4>
      {data.map((comment) => (
        <li key={comment.id}>
          {comment.email}: {comment.body}
        </li>
      ))}
    </>
  );
}
```

#### 4-1. Test

- 위와 같이 작성하면 포스트에 대한 query를 식별하지 못해 동일한 댓글 목록이 보여진다.
- 이를 해결하기 위해 `key`를 작성할 때, 의존성 배열로 작성하여 `key`에 대한 식별자를 달리할 수 있으며, query에서 이를 인식해 `key`가 실행되기 위해 새로운 요청을 보내 데이터를 받아온다.

```jsx
const { data, ...중략 } = useQuery(["comment", post.id], () =>
  fetchComments(post.id)
);
```

#### 기억할 것

- 각 데이터의 의존성 배열을 추가해 query에서 데이터를 불러올 때 식별할 수 있도록 한다.
  - => 식별이 가능해야 다른 이벤트를 발생시켰을 때 그에 맞는 데이터를 다시 요청할 수 있다.
- useQuery에 작성하는 비동기 함수는 값을 전달하거나, 바로 실행이 가능하다.
  - => `useQuery(["example", example],() => exampleFunc(example.data))`
