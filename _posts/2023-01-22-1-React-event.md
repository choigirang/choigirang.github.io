---
layout: post
title: React 3장 - event
author: admin
date: 2023-01-22 00:00:00 +900
lastmod: 2023-01-22 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]  # 대문자로 작성
tags: [react, 리액트, map, jsx, event]
---
# React
## event
- React 엘리먼트에서 이벤트를 처리하는 방식은 DOM 엘리먼트를 처리하는 방식과 비슷하다.
- 이벤트는 소문자 대신 카멜 케이스를 사용한다.
- JSX를 활용하여 문자열이 아닌 함수로 이벤트 핸들러를 전달한다.
  
```jsx
// DOM
<button onclick="activateLasers()">
    Activate Lasers
</button>

// React
<button onClick={activateLasers}>
    Activate Lasers
</button>
```

- React에서는 `false`로 이벤트를 방지할 수 없으며,반드시 `preventDefault`를 호출해야 한다.
- `preventDefault`를 사용하여 이벤트의 기본실행(`submit`은 새로고침)을 막는다.
- 모든 이벤트 핸들러는 이벤트를 동일하기 처리하기 위해 이벤트 객체를 전달받는다.
- 전달인자로 입력되는 `e`는 이벤트 객체를 의미한다.
  - `exampleFunc`의 `e`는 `onSubmit`을 의미하며, `<button>`이 `submit`될 때, 발생한다.
  
```jsx
function Example(){
    function exampleFunc(e){
        e.preventDefault();
    }

    return (
        <form onSubmit={exampleFunc}>
            <button type="submit">SUBMIT</button>
        </form>
    )
}
```

#### 예시


```jsx
import React from "react"
import Header from "./Header.js"

function App (){
    return(
        <div>
            <Header title="React" onChangeMode={function(){
                alert("Header");
            }}></Header>
        </div>
    )
}
```


```jsx
import React from "react"

function Header(props){
    return (
        <header>
            <h1>
                <a href="#" onClick={function(event){
                    event.preventDefault();
                    props.onChangeMode();}
                }>{props.title}</a>
            </h1>
        </header>
    )
}

export default Header
```
  
- `props`로 전달된 `{ title, onChangeMode }`를 `props.title, props.onChangeMode` 로 작성했다.
- `a`태그의 기본 이벤트인 링크 이동을 `preventEvent`로 방지하고 경고창을 실행시킨다.

#### 예시 2
```jsx
import React from "react"
import Nav from "./Nav.js"

function App()  {
    const list = [
        {id: 1, title: "html", body: "html is"},
        {id: 2, title: "css", body: "css is"},
        {id: 3, title: "java", body: "java is"},
    ]

     return (
        <div>
            <Nav list={list} onChangeMode={(id) => {
                alert(id)
            }}></Nav>
        </div>
     )
}
```
  

```jsx
import React from "react"

function Nav(props){
    const navList = [];

    for(let i=0; i<props.topics.length; i++){
        let t = props.topics[i];
        navList.push(
            <li key={t.id}>
                <a id={t.id} href={`/read/` + t.id} onClick={evnet => {
                    event.preventDefault();
                    props.onChangeMode(event.target.id)
                }}>{t.title}</a>
            </li>
            )
    }

    return (
        <nav>
            <ul>
                {navList}
            </ul>
        </nav>
    )
}
```
- `App` 컴포넌트를 통해 `Nav` 컴포넌트로 `topics` 와 `onChangeMode` 함수를 넘겨준다.
- `Nav` 컴포넌트는 새로 선언된 `navList` 배열에 `for`문을 이용하여 `li` 태그를 삽입한다.
- `preventDefault`로 `a` 태그의 기본 동작인 '새 창 열기' 를 막고 `props`로 전달된 `onChangeMode`를 실행시킨다.
- `onChangeMode`에 전달되는 `event.target`은 이벤트를 발생시킨 주체를 의미하는데, 이 주체는 `a` 태그로, `a` 태그의 `id`를 불러온다.