---
layout: post
title: React 24장 - Disney Plus App (1)
author: admin
date: 2023-02-26 00:00:00 +900
lastmod: 2023-02-26 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx]
---

# React

## 컴포넌트 구조 짜기

![image](https://user-images.githubusercontent.com/118104644/221443293-6e033c90-7067-4892-ba9e-ff2937d0e5e1.png)

- 영화 데이터를 받아올 API.
- Nav 컴포넌트, Navigation 역할을 한다.
- Banner 컴포넌트, 영화의 이미지 및 정보를 보여주는 역할을 한다.
- Container 컴포넌트, 카테고리 안에서 공식 동영상을 재생시킨다.
- Category 컴포넌트, 배너 밑에 위치하며 배급사의 정보를 나타낸다.
- Raw 컴포넌트, 영화의 정보들을 펼쳐준다.

- src
  - api
    - axios.js
    - request.js
  - Components
    - Nav.js
    - Banner.js
    - Banner.css
    - Container.js
    - Category.js
    - Row.js
    - Row.css
  - App.js
  - App.css

### api/axios.js

- `axios`를 통해 API를 받아온다.
- `npm install axios`
- The Movie API에서 영화 정보를 받아온다.
- 요청마다 `params`값이 조금 다르고, URL은 같기 때문에, 이를 반복적으로 사용하지 않기 위해 하나의 인스턴스에 담아 사용한다.

```jsx
import axios from "axios"

const instance = axios.create({
    baseURL : "https://api.themoviedb.org/3"
    params : {
        api_key : //api_key가 들어간다.
        language : "ko-KR",
    }
})

export default instance
```

### api/request.js

- 받아올 데이터에 따라 URI가 조금씩 다르기 때문에 이에 대한 값을 변수로 미리 선언해준다.
- 현재 상영, 평점순 등 다양한 설정에 따라 보여줄 영화를 다르게 하기 위함이다.

```jsx
const requests = {
  fetchNowPlaying: "movie/now_playing",
  fetchTrending: "/trending/all/week",
  fetchTopRated: "/movie/top_rated",
  fetchActionMovies: "/discover/movie?with_genres=28",
  fetchComedyMovies: "/discover/movie?with_genres=35",
  fetchHorrorMovies: "/discover/movie?with_genres=27",
  fetchRomanceMovies: "/discover/movie?with_genres=10749",
  fetchDocumentaries: "/discover/movie?with_genres=99",
};

export default requests;
```

### components/Nav.js

- 스크롤이 내려감에 따라 이벤트를 발생시키기 위해서 상태값을 지정해준다.
  - `show`는 scrollY 좌표에 따라 `true`와 `false`값을 가진다.
- `useEffect`를 통해 컴포넌트가 마운트될 때 렌더링해준다.
  - 브라우저에서 스크롤이라는 이벤트가 발생했을 때, `scrollY`의 값을 갖는다.
  - 이 값이 50을 넘어가게 되면 `setShow`를 통해 상태값을 `true`로 지정해주고, `styled`애서 `props`를 넘겨준다.
    - `porps`의 값이 있으면 `#fff`, 없으면 `transparent`로 값을 바꿔서 투명과 흑백의 배경색을 오가게 한다.
- 컴포넌트가 언마운트 될 때 실행되는 코드를 반환하는데, `removeEventListener`를 통해 이벤트 리스너를 제거한다.
  - 이벤트를 제거하지 않으면, 이벤트가 계속 호출되어 `scrollY`값을 계속 계산하기 때문에 제거한다.

```jsx
import React, { useEffect, useState } from "react";
import styled from "styled-components";

const Nav = () => {
  const [show, setShow] = useState(false);

  useEffect(() => {
    window.addEventListener("scroll", () => {
      if (window.scrollY > 50) setShow(true);
      else setShow(false);
    });
    return () => {
      window.removeEventListener("scroll", () => {});
    };
  });

  return (
    <NavWrapper show={show}>
      <Logo>
        <img
          alt="Disney Plus Logo"
          src="/images/logo.svg"
          onClick={() => (window.location.href = "https://www.disneyplus.com")}
        ></img>
      </Logo>
    </NavWrapper>
  );
};

export default Nav;

const NavWrapper = styled.nav`
  position: fixed;
  left: 0;
  right: 0;
  top: 0;
  height: 70px;
  background-color: ${(props) => (props.show ? "#fff" : "transparent")};
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 36px;
  letter-spacing: 16px;
  z-index: 1000;
`;

const Logo = styled.a`
  padding: 0;
  width: 80px;
  margin-top: 4px;
  max-height: 70px;
  font-size: 0px;
  display: inline-block;
  cursor: pointer;

  img {
    display: block;
    width: 100%;
  }
`;
```

### src/App.js

- App 컴포넌트 안에 작성이 완료된 Nav, Banner 컴포넌트를 담고 Container 컴포넌트로 한번 감싸준다.
- Container은 `styled`에 의해 `main`태그를 갖고 있으며, 가상 선택자를 사용한다.
  - 가상 선택자는 `before`와 `after`를 갖고 있는데, `before`는 이전에 새로운 요소가 들어오고, `after`은 이후에 새로운 요소가 들어온다고 생각하면 된다.
  - `after`에 의해 `main`의 뒤에는 `url`을 가진 배경이 들어가게 된다.
  - 가상 선택자를 사용하면 없는 것을 임의로 만들어 주는 것이기 때문에 `content:""`를 넣어줘야 한다.

```jsx
import "./App.css";
import styled from "styled-components";
import Nav from "./components/Nav";
import Banner from "./components/Banner";

const Container = styled.main`
  position: relative;
  min-height: calc(100vh - 250px);
  overflow-x: hidden;
  display: block;
  top: 72px; // Nav 보다 아래에 위치
  padding: 0 calc(3.5vw + 5px);

  &::after {
    background: url("/images/home-background.png") center center / cover
      no-repeat fixed;
    content: "";
    position: absolute;
    inset: 0;
    opacity: 1;
    z-index: -1;
  }
`;

function App() {
  return (
    <Container>
      <Nav />
      <Banner />
    </Container>
  );
}

export default App;
```
