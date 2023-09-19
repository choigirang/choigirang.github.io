---
layout: post
title: React 47장 - React-Query (4)
author: admin
date: 2023-04-27 00:00:00 +900
lastmod: 2023-04-28 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, usequery, query, hooks]
---

# React

## React Query

#### Eslint-plugin

- React 훅을 검사하는 플러그인이다 (useState, useEffect, useContext, useCallback 등).
- 훅들을 올바르게 사용하고 있는지 검사한다.

```node
npm install eslint-plugin-react-hooks --save-dev
```

- React Query 또한 플러그인을 사용하여 규칙을 검사할 수 있고 오류와 버그를 방지하며 코드의 일관성을 유지한다.
- useQuery 훅에서 요청에 대한 캐시를 지정하지 않거나 적절한 데이터 유형을 사용하지 않을 때 메시지를 출력한다.

#### TypeScript

- 맨 위에 `//@ts-nocheck` 를 작성하면 타입 검사를 무시한다.
- 특정 코드의 타입 검사를 무시하고 싶다면 `// @ts-ignore`을 작성하면 된다.

#### Axios Instance

- axios 또한 instance를 만들어서 사용할 수 있다.

```jsx
export const baseUrl = "http://localhost:3030";

const config: AxiosRequestConfig = { baseURL: baseUrl };
export const axiosInstance = axios.create(config);

async function getTreatments(): Promise<Treatment[]> {
  const { data } = await axiosInstance.get("/treatments");
  return data;
}
```

#### Chakra Ui

[공식문서 Chakra](https://chakra-ui.com/docs/components)

- `Chakra UI` 라이브러리는 재사용 가능한 일종의 도구 키트이다.
- 여러 가지 기능을 사용할 수 있으며 로딩을 나타내는 `Spinner, Box, Divider, ControlBox...`등 다양한 컴포넌트를 불러와서 사용할 수 있다.
- 설치

```node
npm i @chakra-ui/react @emotion/react @emotion/styled framer-motion
npx create-react-app my-app --template @chakra-ui/typescript
```

- 설치 후 `chakra-ui`의 ChakraProvider 컴포넌트로 최상위 컴포넌트를 감싸 하위 컴포넌트에서 사용할 수 있다.

```jsx
import {ChakraProvider} from "@chakra-ui/react"
import {theme} from "example/theme';

function App (){
  return (
    <ChakraProvider theme={theme}>
      <ExampleComponents/>
    </ChakraProvider>
  )
}
```

#### 1. Query의 data

- Queryhook을 다른 컴포넌트에서 불러와 사용하여 map을 활용했을 때, 반환하는 동안의 데이터를 추적할 수 없어 에러가 발생한다.

```jsx
// 쿼리 키를 사용할 hooks
export const queryKeys = {
  example: "example1",
  example2: "example2",
  example3: "example3",
};

// useCustomToast
export function useCustomToast() {
  return useToast({
    isClosable: true,
    variant: "subtle",
    position: "bottom",
  });
}

// exampleQuery
import { queryKeys } from "ex.../...";
import { useCustomToast } from "ex.../...";

async function getTreatments() {
  const { data } = await axiosInstance.get("/treatments");
  return data;
}

export function exampleQuery() {
  const example = useCustomToast();

  const fallback = [];
  // data를 추적하는 동안 map으로 사용될 컴포넌트에서 에러를 발생시키기 때문에
  // 임시 데이터를 넣어준다.
  const { data = fallback } = useQuery(queryKeys.example, getTreatments, {
    onError: (error) => {
      const title =
        error instanceof Error
          ? error.message
          : "error connecting to the server";
      toast({ title, status: "error" });
      // 에러 처리
    },
  });
}
```

#### 2. 전역 오류 지정

- 전역 오류를 설정하였기 떄문에 위의 `exampleQuery`함수의 error 부분을 전부 지워도 오류가 발생에 대한 처리가 가능하다.
-

```jsx
function queryErrorHandler(error: unknown): void {
  const title =
    error instanceof Error ? error.message : 'error connecting to server';

  toast.closeAll();
  // Toast가 점차 쌓이기 때문에 중복 방지
  toast({ title, status: 'error', variant: 'subtle', isClosable: true });
}

export const queryClient = new QueryClient(
    defaultOptions: {
        queries:{
            onError : queryErrorHandler
        }
    }
);

```
