---
layout: post
title: React 38장 - React.lazy()와 Suspense
author: admin
date: 2023-03-23 00:00:00 +900
lastmod: 2023-03-23 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, lazy, suspense]
---

# React

## 코드 분할

- 리액트 앱들은 대부분 Webpack, Rollup과 같은 툴을 사용해 번들링한다.
- 이렇게 하면 HTML 웹 페이지에 JavaScript를 쉽게 추가할 수 있다.
- 번들된 앱은 모든 JavaScript가 한 곳에 있기 때문에 페이지에 설정하는 데 필요한 호출 수가 적은 링크 태그 하나만 필요하게 된다.
- 번들링을 하게 되면 특정 지점에서 코드를 해석하고 실행하는 정도가 느려지게 되며, 모던 웹으로 발전하면서 점점 DOM을 다루는 정도가 정교해지며 JavaScript 코드 자체가 방대히지고 무거워졌다.
  ![image](https://user-images.githubusercontent.com/118104644/227123115-e3a0f6f0-96af-438e-9b88-8e67519067b1.png){:.center}

- 번들을 나눈 뒤 필요한 코드만 불러올 수 있다면 실행하는 정도가 느려졌는지 파악해서 지금 필요한 코드만 불러오고 나중에 필요한 코드를 나중에 불러와 속도를 높일 수 있다.
- 코드 분할의 핵심 아이디어이며, 번들이 거대해지는 것을 방지하기 위한 좋은 해결 방법은 번들을 물리적으로 나누는 것이다.
- 런타임 시 여러 번들을 동적으로 만들고 불러오는 것으로 Webpack,Rollu과 같은 번들러가 지원하는 기능이다.
- 이를 통하여 대규모 프로젝트의 앱인 경우에도 페이지의 로딩 속도를 개선할 수 있게 된다.
  ![image](https://user-images.githubusercontent.com/118104644/227123513-256e5b6d-e1d6-40fd-915b-41406dc5172e.png){:.center}

### 번들 분할 혹은 줄이는 법

- 번들링 되는 파일에는 앱을 만들면서 npm을 통해 다운받는 서드파티 라이브러리도 포함 된다.
- 서드파티 라이브러리는 개인 개발자나 프로젝트 팀, 혹은 업체 등에서 개발하는 라이브러리로 제 3자 라이브러리이다.
- 서드파티 라이브러리는 플러그인이나 라이브러리 또는 프레임워크 등이 존재하며, 이 라이브러리를 잘 사용하면 편하고 효율적인 개발을 할 수 있다.
- 서드파티 라이브러리는 사용자에게 다양한 메서드를 제공하기 때문에 코드의 양이 많고, 번들링 시 많은 공간을 차지하게 된다.
- 따라서 사용 중인 라이브러리의 전부를 불러와서 사용하는 것보다 따로 따로 불러와서 사용할 수 있다면 많은 공간을 차지하지 않을 수 있다.

  ```jsx
  // lodash 라이브러리 전체를 불러와서 그 안에 find메서드를 꺼내 쓰고 있다.
  import _ from "loadsh";
  ...
  _.find([]);

  // lodash 라이브러리의 메서드 중 하나인 find를 불러와 꺼내 쓰고 있으며,
  // 웹의 성능에 더 좋다.
  import find from "loadsh/find";

  find([]);
  ```

### React에서의 코드 분할

- 리액트는 SPA(Single Pagenation Application)인데, 사용하지 않는 모든 컴포넌트까지 한 번에 불러오기 때문에 첫 화면이 렌더링 될 때가지의 시간이 오래 걸린다.
- 리액트에서 코드 분할 방법은 **dynamic import**(동적 불러오기)를 사용하는 것이다.
- 코드 파일의 최상위에서 import를 지시하여 사용하고자 하는 라이브러리 및 파일을 불러오는 방법을 사용한다.
- 이를 **static import**(정적 불러오기)라고 한다.

#### Static import

- 기존에는 항상 **import**구문은 문서의 상위에 위치해야 하고, 블록문 안에서는 위치할 수 없는 제약 사항이 있었다.
- 번들링 시 코드 구조를 분석해 모듈을 한 데 모으고 사용하지 않는 모듈은 제거하는 등의 작업을 하는데, 코드 구조가 간단하고 고정되어 있을 때에만 이 작업이 가능하기 때문이다.
- 그러나 구문 분석 및 컴파일해야 하는 스크립트의 양을 최소화 시키기 위해 **dynamic import**구문을 지원한다.

  ```jsx
  // 파일의 최상위에서 import 지시자를 이용해 라이브러리 및 파일을 불러온다.
  import moduleA from "library";

  from.addEventListener("submit", e => {
    e.preventDefault();
    someFunction();
  });

  const someFunction = () => {
    ...
    // 그리고 불러온 라이브러리나 파일을 코드 중간에서 사용한다.
  }
  ```

#### Dynamic import

- **dynamic import**를 사용하게 되면 불러온 `moduleA`가 다른 곳에서 사용되지 않는 경우, 사용자가 form 양식을 통해 제출한 경우에만 가져올 수 있도록 한다.
- **dynamic import**의 `then` 함수를 사용해 필요한 코드만 가져오며, 가져온 코드에 대한 모든 호출은 해당 함수 내부에 있어야 한다.
- 이 방식을 사용하면 번들링 시 분할된 코드를 지연 로딩시키거나 요청 시에 로딩할 수 있다.
- `React.lazy`와 함께 사용할 수 있다.

  ```jsx
  form.addEventListener("submit", (e) => {
    e.preventDefault();
    // 동적 불러오기는 코드의 중간에 불러올 수 있게 된다.
    import("library.moduleA")
      .tehn((module) => module.default)
      .then(someFunction())
      .catch(handleError());
  });

  const someFunction = () => {
    // 모듈 A를 사용한다.
  };
  ```

## React Lazy

- React.lazy()함수를 사용하면 **dynamic import**를 사용해 컴포넌트를 렌더링 할 수 있다.
- 리액트는 SPA이므로 한 번에 사용하지 않는 모든 컴포넌트까지 불러오는 단점을 `React.lazy`를 통해 동적으로 import 할 수 있기 때문에 초기 렌더링 지연시간을 줄일 수 있다.
- `React.lazy` 컴포넌트로 단독으로 쓰일 수는 없고, `React.suspense` 컴포넌트의 하위에서 렌더링 해야 한다.
  ```jsx
  import Component from "./Component";
  // React.lazy로 dynamic import를 감싼다.
  const Component = React.lazy(() => import("./Component"));
  ```

## React Suspense

- Router로 분기가 나누어진 컴포넌트들을 위 코드처럼 lazy를 통해 import하면 해당 path로 이동할 때 컴포넌트를 불러오게 되는데 이 과정에서 로딩하는 시간이 생기게 된다.
- `suspense`는 아직 렌더링이 준비되지 않은 컴포넌트가 있을 때 로딩 화면을 보여주고, 로딩이 완료되면 렌더링이 준비된 컴포넌트를 보여주는 기능이다.

  ```jsx
  // suspense 기능을 사용하기 위해 import 한다.
  import { suspense } from "react";

  const OtherComponent = React.lazy(() => import("./OtherComponent"));
  const AnotherComponent = React.lazy(() => import("./AnotherComponent"));

  function MyComponent() {
    return (
      <>
        <div>
          <Suspense fallback={<div>Loading...</div>}>
            <OtherComponent />
            <AnotherComponent />
          </Suspense>
        </div>
      </>
    );
  }
  ```

## React.lazy와 Suspense의 적용

- 코드 분할을 도입할 곳을 결정하는 것은 까다롭기 때문에, 중간에 적용시키는 것보다 웹 페이지를 불러오고 진입하는 단계인 `Route`에 기능을 적용시키는 것이 좋다.
- 라우터에 `Suspense`를 적용하는 것은 간단한 편이며, 라우터가 분기되는 컴포넌트에서 각 컴포넌트에 `React.lazy`를 사용하여 `import` 한다.
- Route 컴포넌트들을 `Suspense`로 감싼 후 로딩 화면으로 사용할 컴포넌트를 `fallback` 속성으로 설정해주면 된다.
- 초기 렌더링 시간이 줄어드는 분명한 장점이 있으나 페이지를 이동하는 과정마다 로딩 화면이 보여지기 때문에 서비스에 따라서 적용 여부를 결정해야 한다.

  ```jsx
  import {Suspense, lazy} from "react";
  import {BrowserRouter as Router, Routes, Route} from "react-router-dom";

  const Home = lazy(() => import("./routes/Home"));
  const About = lazy(() => import("./routes/About"));

  const App = () => (<>
    <Router>
        <Suspense fallback={<div>Loading...</div>}>
            <Routes>
                <Route path="/" element={<Home />}>
                <Route path="/About" element={<About />}>
            </Routes>
        </Suspense>
    </Router>
    </>
  )
  ```
