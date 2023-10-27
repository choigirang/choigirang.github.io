---
layout: post
title: React 63장 - react-query 다시 배우기 (3)
author: admin
date: 2023-10-26 00:00:00 +900
lastmod: 2023-10-27  00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, query, key]
---

# React

[리액트 쿼리 다시 배우기 (2)](<https://choigirang.github.io/posts/02-React-Query-%EB%8B%A4%EC%8B%9C%EB%B0%B0%EC%9A%B0%EA%B8%B0(2)/>)

## React-Query

- 미니 프로젝트를 만들어보다가 `useMutation`을 사용하며 `queryClient.invalidateQueries`을 통해 쿼리 키를 초기화시켜, `mutate`를 통해 글을 작성했을 때 내가 작성한 데이터를 자동으로 받아오려고 했다.
- 문제가 된 부분, 하나의 `useFetch`를 사용하고 있기 때문에 처음에 모든 데이터를 보여줄 때에는 쿼리 키를 `all`로 지정해줬다.
- 그리고 내가 클릭한 카테고리에 대한 데이터를 받아올 때는 쿼리 키를 카테고리로 지정해줬다.
- 여기서 문제점은 `invalidateQueries`로 쿼리 키를 초기화 해주더라도 개별 데이터를 관리하는 것이 잘못됐다.

```tsx
// example
function Example(category: { category?: string }) {
  const posts = useQuery<AxiosResponse<ApiStackData[]>>(
    [stack !== "" ? stack : ""],
    () => api.get(`/posts/${stack !== "" ? stack : "all"}`)
  );

  return { posts };
}
```
