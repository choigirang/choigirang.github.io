---
layout: post
title: React 46장 - React-Query (3)
author: admin
date: 2023-04-26 00:00:00 +900
lastmod: 2023-04-26 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags:
  [react, components, jsx, usequery, query, useinfinitequery, infinitescroll]
---

# React

## React Query

### 1. useInfiniteQuery

- `useInfiniteQuery`는 무한 스크롤과 같은 데이터 로딩에 사용하는 ReactQuery의 훅이다.
- 이전 페이지의 데이터를 가져와 새로운 데이터와 병합하는 방식이다.
- `getNextPageParam` 등의 옵션으로 다음 페이지의 데이터를 가져오기 위한 파라미터를 반환하는 함수를 정의할 수 있다.
- `useInfiniteQuery`에서 객체는 두 개의 프로퍼티를 가지고 있다.
  - `pages` : 데이터 페이지 객체의 배열인 페이지
    - 모든 쿼리는 페이지 배열에 고유한 요소를 가지고 있고, 요소는 해당 쿼리에 대한 데이터에 해당한다.
    - 페이지가 진행되면서 쿼리도 바뀐다.
  - `pageParams` : 각 페이지의 매개 변수가 기록된다. 많이 사용하지는 않는다.
    - 검색된 쿼리의 키를 추적한다.
- 예시 코드
  - `pageParam`은 쿼리 함수에 전달되는 매개변수이다.
  - 쿼리 키를 넣어, 쿼리 함수가 실행되는 동안 객체를 구조 분해한 `pageParam`을 사용한다.
  - 그리고 첫 번째 Url로 정의한 Url을 기본값으로 설정한다.
  - 함수는 defalutUrl을 기본값으로 하는 pageParam을 사용해서 fetchUrl 함수를 실행한다.
  - fetchUrl에 pageParam을 넘긴 페이지를 찾아 데이터를 가져온다.
  - `Pagnation(["posts",currentPage] , currentPage 상태를 업데이트)`과 달리 ReactQuery가 `pageParam`의 값을 유지한다.
  - `useInfiniteQuery`의 옵션
    - `getNextPageParam`: (lastpage, allPages) , 다음 페이지로 가는 방식을 정의하는 함수이다.
      - 마지막 페이지에 대한 또는 모든 페이지에 대한 데이터를 가져온다.
    - `fetchNextPage`: 사용자가 더 많은 데이터를 요청할 때 호출하는 함수이다.
      - 더 많은 데이터를 요청하는 버튼을 누르거나 데이터가 소진되는 지점을 누르는 경우이다.
    - `hasNextPage`: getNextPageParam 함수의 반환 값을 기반으로 하며, 이 프로퍼티를 useInfiniteQuery에 전달하여 마지막 데이터에 대한 처리를 지시한다.
      - falsy한 값을 부여하여 데이터를 더 이상 받아오지 않도록 한다.
    - `isFetchingNextPage`: useQuery의 fetching과 비슷하지만, 다음 페이지를 가져오는지 아니면 일반적인 fetching인지를 구별할 수 있다.
    ```jsx
    useInfiniteQuery("sw-people", ({ pageParam = defaultUrl }) =>
      fetchUrl(pageParam)
    );
    ```

```jsx
async function fetchPosts({ pageParam = 0 }) {
  const res = await fetch(`/api/posts?page=${pageParam}`);
  const data = await res.json();
  return {
    posts: data.posts,
    nextPage: data.nextPage,
  };
}
// useInfinityQuery를 쓰면 함수를 일단 실행함
// params는 0임
// 그 다음 getNextPageParams실행
// lastPage = 제일 최근에 가져온 페이지, 두번째 인자가 필요함 여태까지 가져온 페이지들 (lastPage,[allPage])
// data 한 번 찍어보기
// getNextPageParams에서 리턴한 값이 fetchPosts에서 리턴한 값이 pageParam으로 들어감
// 페이지가 없을 때는 null을 리턴하면 더 이상 hasNextPage가 falsy한 값으로 지정하면
// 데이터를 더 이상 받아오지 않는다.

function PostList() {
  const { data, isLoading, isFetchingNextPage, hasNextPage, fetchNextPage } =
    useInfinityQuery(["posts"], fetchPosts, {
      getNextPageParams: (lastPage, "두번째 인자") => lastpage.nextPage,
    });

  if (isLoading && !isFetchingNextPage) {
    return <div>Loading...</div>;
  }

  return (
    <div>
      {data.pages.map((page) => (
        <React.Fragment key={page.pageNumber}>
          {page.posts.map((post) => (
            <div key={posts.id}>{post.title}</div>
          ))}
        </React.Fragment>
      ))}
      {hasNextPage && (
        <button onClick={() => fetchNextPage()} disabled={isFetchingNextPage}>
          {isFetchingNextPage ? "Loading more..." : "Load More"}
        </button>
      )}
    </div>
  );
}
```

- 예제 코드
  - `pageParam`이 기본값인 `initialUrl`인 이유는 처음 시작할 데이터 주소를 말한다.
  - `getNextPageParam`은 fetchNextPage와 hasNextPage를 결정하는데, `https://swapi.dev/api/people/`에 들어있는 next 키의 값인 `https://swapi.dev/api/people/?page=2`를 실행하여 pageParam에 새로운 주소값이 들어간다.
  - 이렇게 api에서 받아온 데이터는 data에 들어가고, next가 없을 경우 undefined를 설정해, hasNextPage에 undefined가 부여된다.
  - undefined에 대해 실행하지 않으므로 다음 데이터를 받아오지 않는다.

```jsx
const initialUrl = "https://swapi.dev/api/people/";
const fetchUrl = async (Url) => {
  const res = await fetch(url);
  return res.json();
};

function InfinitePeople() {
  const { data, fetchNextPage, hasNextPage } = useInfiniteQuery(
    "sw-people",
    ({ pageParam = initialUrl }) => fetchUrl(pageParam),
    {
      getNextPageParam: (lastPage) => lastPage.next || undefined,
    }
  );
}
```

#### 1.1 useInfiniteQuery 사용하기

- `InfinityScroll`이라는 컴포넌트를 사용하여 무한 스크롤을 구현한다.

```node
npm install react-infinite-scroller
```

- `infinite scroller`라는 툴은 `useInfiniteQuery`와 호환이 잘 되며 두 개의 프로퍼티가 있다.

  - `loadMore={fetchNextPage}` : 데이터가 더 필요할 때 불러오며 `useInfiniteQuery`의 fetchNextPage 함숫값을 이용한다.
  - `hasMore={hasNextPage}` : `useInifiteQuery`에서 나온 객체를 해체한 값을 사용한다.
  - 무한 스크롤 컴포넌트는 스스로 페이지의 끝에 도달했음을 인식하고 fetchNextPage를 불러오는 기능이 있다.
  - 데이터 프로퍼티에서 데이터에 접근할 수 있으며 useInfiniteQuery에서 나온 객체를 이용한다.
    - 배열인 페이지 프로퍼티를 이용해서 그 페이지 배열의 맵을 만들어 데이터를 표시할 수 있게 해준다.
    - `data.pages[a].results`

- 예제 코드

```jsx
import InfiniteScroll from "react-infinite-scroller";
import { useInfiniteQuery } from "react-query";

const initialUrl = "https://swapi.dev/api/people/";
const fetchUrl = async (Url) => {
  const res = await fetch(Url);
  return res.json();
};

function InfinitePeople() {
  const {
    data,
    fetchNextPage,
    hasNextPage,
    isLoading,
    isFetching,
    isError,
    error,
  } = useInfiniteQuery(
    "sw-people",
    ({ pageParam = initialUrl }) => fetchUrl(pageParam),
    {
      getNextPageParam: (lastPage) => lastPage.next || undefined,
    }
  );

  if (isLoading) {
    return <div className="loading">Loading...</div>;
    // style의 position 값을 고정시켜 스크롤을 하더라도 Loading 메시지가 보여지게 한다.
  }
  if (isError) {
    return <div className="error">{error.toString()}</div>;
  }

  return (
    <>
      {isFetcing && <div className="loading">Loading...</div>}
      <InfiniteScroll loadMore={fetchNextPage} hasMore={hasNextPage}>
        {data.page.map((pageData) =>
          pageData.results.map((person) => (
            <Person
              key={person.name}
              name={person.name}
              hairColor={person.hair_color}
              eyeColor={person.eye_color}
            />
          ))
        )}
      </InfiniteScroll>
    </>
  );
}
```

- 페이지의 끝에 도달했을 때는 `loadMore`을 통해 `fetchNextPage`를 실행시키고, 데이터가 더 있는지 확인하기 위해 `hasMore`을 통해 `hasNextPage`가 있는지 확인한다.
- data는 `pages`와 `pageParams`를 가지고 있다.
- isLoading을 활용하지 않을 경우, 데이터를 불러오는 과정에서 data가 없다고 인식하여 우리가 설정한 undefined를 전달하기 때문에 isLoading을 활용한다.
- 데이터를 가져오는 중에는 가져오는 중의 문구가 필요하기 때문에 isFetching을 사용해 화면에 데이터를 불러오는 문구를 사용자에게 알려준다.

```jsx
{ pageParams: [undefined],
  pages: Array(1)
    0:
      count: 82
    next: "https://swapi.dev/api/people/?page=2"
    previous: null
    results: (10) [{…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}]
}
```

#### 2. 연습

- `getNextPageParam`을 실행하여 처음 시작 주소인 "https://swapi.dev/api/species/"의 데이터를 가져온다.
- 받아온 데이터의 lastPage의 next의 주소값이 pageParam이 되고, 새로운 주소값으로 `fetchSpecies`를 실행한다.
- 그렇게 받아온 값은 다시 `data`가 된다.
- 만약 다음 페이지가 없을 경우 `hasNextpage`가 undefined가 된다.
- 이 값을 `InfiniteScroll`에서 사용하여 스크롤에 도달했을 때 `fetchNextPage`를 실행한다.

```jsx
import InfiniteScroll from "react-infinite-scroller";
import { useInfiniteQuery } from "react-query";

const initialUrl = "https://swapi.dev/api/species/";
async function fetchSpecies(Url) {
  const res = await fetch("https://swapi.dev/api/species/");
  return res.json();
}

function InfiniteSpecies() {
  const {
    data,
    fetchNextPage,
    hasNextPage,
    isLoading,
    isFetching,
    isError,
    error,
  } = useinfiniteQuery(
    "example",
    ({ pageParam = initialUl }) => fetchSpecies(pageParam),
    {
      getNextPageParam: (lastPage) => lastPage.next || undefined,
    }
  );

  // 이하 생략
  <InfinitiScroll loadMore={fetchNextPage} hasMore={hasNextPage} />;
}
```
