---
layout: post
title: React 5장 - Router
author: admin
date: 2023-01-24 00:00:00 +900
lastmod: 2023-01-24 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]  # 대문자로 작성
tags: [react, components,router, ssr, csr, client, server, rendering]
---

# React

## SPA

- Single-Page-Application의 약자이다.
- 사용자에 요청에 페이지 전체를 바꿔서 보여주는 방식이 아닌 필요한 부분만을 로딩하는 방식이다.
- 기존 브라우저는 웹페이지를 미리 준비해두었다가, 서버 측에서 데이터를 전달받아 렌더링하는 방식이었다.
- 규모가 커지고 사용자와의 상호작용이 많아짐에 따라 기존의 서버 방식의 속도 저하 문제가 발생했다. 이를 해결하기 위한 것이 SPA방식이다.
- CSR방식이다 ([SSR과 CSR](https://choigirang.github.io/CS/3-CS-SSR%EA%B3%BC-CSR/){: target="\_blank"})

## Router

- 사용자가 요청한 URL에 따라 해당 URL에 맞는 페이지를 보여주는 것이다.
- 여러 라우팅 관련 라이브러리 중 `React Router`가 가장 많이 사용된다.
- 리액트 SPA에서는 경로에 따라 다른 뷰를 보여줄 수 있다.
- 라우터 라이브러리는 크게 세가지로 구분된다.
  - BrowserRouter : 컴포넌트의 최상위에 작성되어 React Router의 컴포넌트를 활용할 수 있도록 한다.
  - Routes, Route : 경로를 매칭해주며 Routes 컴포넌트가 경로가 일치하는 하나의 Route만 렌더링한다.
  - Link : `a`태그와 비슷한 역할을 하며, Link 컴포넌트를 누르면 화면을 변경시키는 역할을 한다.
    - `a`태그는 페이지 전환 시, 처음부터 렌더링을 시키는 새로고침 현상이 있지만, Link컴포넌트는 페이지 전환을 방지하는 기능이 내장되어 있어 SPA구현에 적합하다.

## Router 사용

- `npm install react-router-dom --save`를 입력하여 Router를 설치한다.
- package.json 파일에 `dependencies` 중 `react-router-dom`이 있는지 확인한다.
- Router 라이브러리를 최상위 컴포넌트에 작성한다.
- 각 페이지로 이동할 url주소를 입력하고 페이지에 해당하는 컴포넌트들을 연결한다.

```jsx
import React from "react"
import {BrowserRouter,Routes,Route,Link} from 'react-router-dom'
import Home from "./Home.js"
import MyPage from "./MyPage.js"
import Board from "./Board.js"

function App(){
    return (
        <BrowserRouter>
            <Link to="/">Home</Link>
            <Link to="/mypage">Mypage</Link>
            <Link to="/board">Board</Link>

            <Routes>
                <Route path="/" element={<Home />}>
                <Route path="/mypage" element={<MyPage />}>
                <Route path="/board" element={<Board />}>
            </Routes>
        <BrowserRouter>
    )
}
```

#### 예시

- index.js

```jsx
import React from "react"
import ReactDOM from "react-dom"
import App from "./App.js"
import {BrowserRouter} from "react-router-dom"

ReactDOM.render(
    <BrowserRouter>
        <App />
    </BrowserRouter>
    document.getElementById("root")
)
```

- Home 컴포넌트

```jsx
import React from "react";

function Home() {
  return (
    <div>
      <h1>HOME</h1>
    </div>
  );
}

export default Home;
```

- About 컴포넌트

```jsx
import React from "react";

function About() {
  return (
    <div>
      <h1>About</h1>
    </div>
  );
}

export default About;
```

- App 컴포넌트

```jsx
import { Route, Routes } from "react-router-dom";
import Home from "./Home.js";
import About from "./About.js";

function App() {
  return (
    <Routes>
      <Routes path="/" element={<Home />} />
      <Routes path="/About" element={<About />} />
    </Routes>
  );
}

export default App;
```
