---
layout: post
title: React 8장 - Styled-Component
author: admin
date: 2023-02-18 00:00:00 +900
lastmod: 2023-02-18 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]  # 대문자로 작성
tags: [react, components, styled-components, ui]
---
# Styled Component
- 기존 DOM을 만드는 방식인, css나 scss 파일을 따로 생성하고 이를 불러와 사용하는 것이 아닌 컴포넌트 내부에 style을 작성하는 것을 말한다.
- style을 따로 불러와 사용하지 않기 때문에 css의 속성이 전역으로 중첩되어 적용되는 것을 방지한다는 장점이 있다.  
`npm install Styled-Component`
- 라이브러리를 설치 후 `package.json`파일에 다음의 코드를 추가하여 여러 버전의 Styled-Component가 설치되어 발생하는 문제를 줄여주는 것을 권장하고 있다.
```jsx
{
    "resolutions":{
        "styled-component": "^5"
    }
}
```


- 사용할 파일에 Styled-Component를 불러와준다.

## Styled-Component 만들기
- `const 컴포넌트명 = Styled-Component 만든다.
- 만들고자 하는 컴포넌트의 render 함수 밖에서 작성한다.

## 스타일에 props 적용하기
- Styled-Component는 변수에 의해 스타일을 바꿀 수 있다는 장점이 있다.
- 예를 들어, `email` 이라는 `state` 값에 따라 `ExampleWrap` 컴포넌트에 `prop` 으로 내려주는 `active` 값이 `true` 혹은 `false` 로 바뀐다.
- Styled-Component 는 내부적으로 `props`를 받을 수 있고, `props` 에 따라 스타일을 변경할 수 있다.

## 예시
```jsx
import React,{ useState } from "react";
import styled from "styled-component";

const ExampleWrap = () => {
    const [email, setEmail] = useState("");

    return(
        <ExampleWrap active={email.length}>
            <Button>Hello</Button>
            <NewButton color="blue">new Button</NewButton>
        </ExampleWrap>
    );
}

const ExampleWrap = styled.div`
  background: ${({ active }) => {
    if (active) {
      return "white";
    }
    return "#eee";
  }};
  color: black;
`;

const Button = styled.button`
    width: 200px;
    padding: 30px;
`;
// Button 컴포넌트 상속
const NewButton = styled.button`
    color: ${props => porps.color || "red"}
`;
// NewButton 컴포넌트에 color가 있으면
// 입력된 color 값을 사용,
// 없으면 'red' 사용

export default Example;
```

## 컴포넌트 재활용하여 새로운 컴포넌트 만들기
- [sandbox 예제보기](https://codesandbox.io/s/purple-flower-oskll?from-embed){: target="_blank"}


```jsx
import React from "react"
import styled from "styled-components"

const BlueButton = styled.button`
    background-color: blue;
    color: white;
`;

const BigBlueButton = styled(BlueButton)`
    padding: 10px;
    margin-top: 10px;
`

const BigRedButton = styled(BigBlueButton)`
    background-color: red;
`

export default function App(){
    return (
        <>
            <BlueButton>Blue Button</BlueButton>
            <br />
            <BlueBlueButton>Big Blue Button</BlueBlueButton>
            <br />
            <BlueRedButton>Big Red Button</BlueRedButton>
            <br />
        </>
    )
}
```


## Props 활용하기
- React의 컴포넌트처럼 `props`를 내려줄 수 있다.
- 템플릿 리터럴 문법을 사용하여 `props`를 인자로 받는 함수를 만들어 사용하면 된다.
- [sandbox 예제보기](https://codesandbox.io/s/modern-frost-h4mcu?from-embed){: target="_blank"}



```jsx
import React from "react"
import styled from "styled-components"
import Globalstyle from "./Globalstyle.js"

const Button1 = styled.button`
    background: ${(props) => (props.skyblue?skyblue:white)}
`

export default function App (){
    return(
        <>
            <Globalstyle />
            <Button1>Button1</Button1>
            <Button1 skyblue>Button1</Button1>
        </>
    )
}
```


## Props 값으로 렌더링하기
- `props`의 값을 통째로 활용해서 컴포넌트 렌더링에 활용할 수 있다.
- [sandbox 예제보기](https://codesandbox.io/s/interesting-platform-3ecom?from-embed){: target="_blank"}



```jsx
import React from "react"
import styled from "styled-components"
import GlobalStyle from "./GlobalStyle.js"

const Button1 = styled.button`
    background:${(props)=>(props.color ? props.color : "white")}
`

const Button2 = styled.button`
    background:${(props)=>props.color||"white"};
`

export default function App(){
    return (
        <>
            <GlobalStyle />
            <Button1>Button1</Button1>
            <Button1 color="orange">Button1</Button1>
            <Button1 color="tomato">Button1</Button1>
            <Button2>Button2</Button2>
            <Button2 color="pink">Button2</Button2>
            <Button2 color="turquoise">Button2</Button2>
        </>
    )
}
```


## 전역 스타일 설정하기
- 전역에 스타일을 설정하고 싶다면 `createGlobalStyle`을 불러온다.
- CSS 파일을 작성하듯 설정해주고 싶은 스타일을 작성한다.
- 작성한 `<GlobalStyle>`을 최상위 컴포넌트에 사용하면 전역에 스타일이 적용된다.

## 응용 hover 이벤트
- [sandbox 예제보기](https://codesandbox.io/s/styled-component-hover-5dgrz9?from-embed){: target="_blank"}



```jsx
import React from "react"
import styled,{createGlobalStyle} from "styled-components"

const Btn = styled.button`
    background : blue;
    border-radius: 1rem;
    transition: 0.5s;
    &: hover {
        background: darkblue;
        color: white;
        transition: 0.5s;
    }
`

export default function App(){
    return (
        <div>
            <GlobalStyle />
            <Btn>Btn</Btn>
            <Btn id="practice">Btn+id</Btn>
        </div>
    )
}
```


## Mixin css props
- `css props` 는 자주 쓰이는 css 속성을 담는 변수이다.
- `css 변수명 = css` 로 작성한다.


```jsx
const flexCenter = css`
    display: flex;
    justify-content: center;
    align-items: center;
    `;

const FlexBox = div`
    ${flexCenter}
    `;
```

## 다른 파일에서 컴포넌트 import 하기
- 해당 파일에서 정의한 css를 `export` 하여 다른 파일에서 `import` 할 수 있다.


```jsx
// Login.jsx
export const LoginContainer = styled.div`
    background: red;
`;

// Other.jsx
import {LoginContainer} from "Login.jsx";

const Other = () => {
    return <LoginContainer></LoginContainer>;
};
```

## 반응형 디자인
- [Styled-Component 를 이용한 반응형 디자인 참조](https://velog.io/@syoung125/CSS-%EB%B0%98%EC%9D%91%ED%98%95-%EC%9B%B9-%EB%94%94%EC%9E%90%EC%9D%B8-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0){: target="_blank"}


```jsx
import React from "react"
import Styled-Componentm "Styled-Component"

const sizes = {
    desktop: 1024,
    tablet: 768
};

// sizes 객체에 따라 자동으로 media 쿼리 함수를 만들어준다.
const media = Object.keys(sizes).reduce((acc,cur)=>(
    acc[label] = (...args) => css`@meadia (max-width: ${sizes[label] / 16}em) {
        ${css(...args)}
    }`;

    return acc;
), {});

const Box = Styled-Component// props로 넣어준 값을 직접 전달해줄 수 있다.
    background: ${props => props.color || "blue"};
    padding: 1rem;
    display: flex;
    width: 1024px;
    margin: 0 auto;
    ${media.desktop`width: 768px;`}
    ${media.tablet`width: 768px;`};
    `;

    
```