---
layout: post
title: React 70장 - Query를 사용한 무한 스크롤
author: admin
date: 2024-02-25 00:00:00 +900
lastmod: 2024-02-25  00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, query, infinitequery]
---

# React

## 무한 스크롤 구현하기

- `react-query`에는 무한 스크롤을 구현할 수 있는 `useInfiniteQuery` 훅이 내장되어 있다.
- 파라미터 값만 변경하여 `useQuery`를 무한정 호출한다.
- `useQuery`가 작동하는 방식과 마찬가지로 `fetch`를 통해 데이터를 가져오고, 특정 조건일 때 `pageParams`를 통해 다음 페이지의 데이터를 가져올 수 있다.
- `useInfiniteQuery`는 세 가지 인자를 받을 수 있는데, 세 번째의 `getNextPageParam`을 통해 다음 페이지에 대한 파라미터를 반환한다.

### 예시

```jsx
export default function useMovieData() {
  async function fetchMovies({ pageParam = 1 }: { pageParam?: number }) {
    const res = await api.get(``, {
      params: {
        page: pageParam,
      },
    });
    return res.data.results;
  }

  return useInfiniteQuery("movies", fetchMovies, {
    getNextPageParam: (lastPage, allPages) => {
      return allPages.length + 1;
    },
  });
}
```

- `axios`인스턴스를 통해 기본 주소값을 설정 후, 추가적으로 들어갈 파라미터를 설정한다.
- 파라미터는 `page=...`식으로 들어가게 된다.
- `page=1, 2, 3` 마다 해당하는 데이터를 반환하며, `getNextPageParam`을 통해 다음 페이지에 대한 숫자를 추가한다.
  - 크게 두 가지의 인자를 받을 수 있는데 `lastPage`는 마지막에 있는 데이터를 의미하며, `allPage`는 호출된 모든 페이지를 의미한다.
  - `lastPage` 같은 경우 한 페이지당 10개의 데이터를 받아온다고 했을 때, 이번 페이지에서 9개의 데이터를 가져왔다면 현재 페이지가 마지막 페이지임을 의미한다.
  - `allPage`는 현재 호출한 페이지가 1번이고 다음 페이지를 받아온다면, 모든 페이지의 갯수는 2일 것이다.
  - 여기서 `return`한 값이 `pageParam`으로 사용되며, `pageParam`의 초깃값을 설정해주지 않을 경우 `undefind`이기 떄문에 초깃값을 설정해준다.
  - `return`으로 더해진 `현재 allPage.length = 1 (초깃값에서 + 1)`이 `pageParam`으로 적용되어 두 번째 페이지의 목록을 불러오는 것이다.

### 구조

```jsx
const { data, fetchNextPage, hasNextPage, isFetchingNextPage } = useMovieData();
```

- `fetchNextPage`를 특정 조건일 때 호출시켜, 다음 페이지의 데이터를 가져온다.
- `getNextPageParam`을 더해 다음 페이지를 호출하는데, 이는 배열 안에 순차적으로 데이터가 추가된다.
  - 현재 데이터 `[1pageData, 2pageData]`, `fetchNextPage` 호출, `[1pageData, 2pageData, 3pageData]`

## getPreviousPageParam & fetchPreviousPage

- 공통적으로 이전 api를 요청할 때 사용될 `pageParam` 값을 정할 때 사용되는데 어떤 상황에서 사용되는지는 잘 모르겠다.

### 예시

```jsx
...
return useInfiniteQuery("movies", fetchMovies, {
    getPreviousPageParam: (firstPage, allPages) => {
      return firstPage.length - 1;
    },
  });
```

- 파라미터 값으로 크게 `firstPage, allPages`를 받으며, `firstPage`는 가장 처음에 있는 페이지의 데이터를, `allPages`는 호출된 모든 페이지를 의미한다.
- 여기서 반환되는 값은 이전 페이지가 호출될 때 `pageParam` 값으로 사용된다.
- `fetchPreviousPage`는 이전 페이지의 데이터를 호출할 때 사용된다.
  - `fetchNextPage`와는 반대로, 데이터는 좌측에 삽입되어 정렬된다.

```jsx
const {..., fetchPreviousPage} = useInfiniteQuery(...)
```

## hasNextPage & hasPreviousPage

- `boolean` 타입을 갖고 있으며, `getNextPageParam, getPreviousPageParam`에 의해 값이 저장된다.
- api 호출에 사용될 `pageParam` 값이 정상적으로 담겨있을 경우, `true`로 값이 없을 경우 `false`로 저장된다.

### 예시

```jsx
return useInfiniteQuery("movies", fetchMovies, {
  getPreviousPageParam: (firstPage, allPages) => {
    return firstPage.length > 1 && firstPage.length - 1;
  },
  getNextPageParam: (lastPage, allPages) => {
    return lastPage.length > 10 && lastPage.length + 1;
  },
});
```

- 이렇게 조건을 작성했지만, 페이지 호출이 발생하면 존재하지 않는 페이지임에도 우선 실행된다.
- 이를 방지하기 위해 사용되는데 `get...Page`를 통해 조건을 걸어주었기 때문에 페이지가 존재할 때만 실행시킬 수 있다.

```jsx
hasNextPage && fetchNextPage;
```

## 사용 예시

```jsx
export default function MovieList() {
  const target = useRef < HTMLDivElement > null;
  const { data, fetchNextPage, hasNextPage, isFetchingNextPage } =
    useMovieData();

  useEffect(() => {
    if (target.current && !hasNextPage) {
      return;
    }
    const observer = new IntersectionObserver(
      (entries) => {
        if (entries[0].isIntersecting) {
          fetchNextPage();
        }
      },
      { threshold: 0.5 }
    );
    if (target.current) {
      observer.observe(target.current);
    }

    return () => {
      if (target.current) {
        observer.unobserve(target.current);
      }
    };
  }, [target.current, data?.pageParams]);

  return (
    <Grid container spacing={4}>
      {data &&
        data.pages.map((page, pageIndex) => (
          <React.Fragment key={pageIndex}>
            {page.map((movie: MovieDetailType) => (
              <MovieInfo key={movie.id} {...movie} />
            ))}
          </React.Fragment>
        ))}
      {isFetchingNextPage && <div>Loading...</div>}
      {!hasNextPage && <div>End of List</div>}
      <div ref={target} />
    </Grid>
  );
}
```

- 브라우저에서 제공하는 API인 `IntersectionObserver`를 통해 특정 요소가 뷰포트와 교차하는지 여부를 판별한다.
  - `threshold`는 대상 요소가 뷰포트 안에 얼마나 들어왔는지를 추적하는데, `0.5 => 50%`
- `entries`는 전달되는 인자로, 배열 안에 첫 번쨰 요소인 `ref`가 `isIntersecting`으로 뷰포트 안에 들어왔는지 여부가 판별되어 `boolean` 값을 리턴한다.
- 이를 통해 마지막 빈 요소인 `div`가 화면에 들어왔는지를 판별하여, 화면에 들어왔다면 `fetchNextPage`를 통해 다음 페이지의 데이터를 받아오게 된다.
