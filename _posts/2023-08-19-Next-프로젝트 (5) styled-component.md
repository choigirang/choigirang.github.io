---
layout: post
title: Next 12장 - styled-components 추가
author: admin
date: 2023-08-19 00:00:00 +900
lastmod: 2023-08-19 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [NEXT]
tags: [next, react, styled-components]
---

# Next

## styled-components

- 프로젝트를 만들다 보면 css를 컴포넌트화하여 간편하게 쓸 수 있는 `styled-components`를 많이 사용하게 된다.
- 그저 `const Example = styled.div`만 할 줄 알았기에 사용법을 좀 더 익히고자 작성하였다.

```jsx
const Example = styled.div`
  display: block;
  width: 100px;
  height: 100px;
`;
```

- 위 형식과 같이 태그를 지정하고, 지정하는 태그의 스타일 컴포넌트 변수명을 선언하여 사용한다.
- 여태까지 위와 같이 사용해왔고, 이에 추가해봐야 `export`를 통해 공통되는 스타일을 적용해서 사용한다는 것밖에 없었다.
- 또는 조건부에 따른 `props`를 활용한 스타일 적용을 할 때나 활용하였다.

```jsx
const Example /= styled.div`
    color: ${props => props.color ? "red" : "green"};
    background-color: ${props => props.bg ? "#000000" : "#000001"};
`

<Example color={color} bg={bg}/>
```

### 컴포넌트 중첩

- 매번 단일 컴포넌트로만 사용하는 방법이 아닌, 중첩된 컴포넌트를 활용할 수 있다.

```jsx
const Child = styled.div`
  width: 100px;
  height: 100px;
  background-color: black;
  color: red;
`;

const Parent = styled.div`
  ${Child}:hover {
    color: blue;
  }
`;
```

### 컴포넌트 확장

- 이미 작성된 컴포넌트의 스타일을 받아 사용하며, 다른 스타일을 추가할 수 있다.

```jsx
const Box = styled.div`
  width: 100%;
  height: 100%;
  background-color: black;
  color: red;
`;

const SimilarBox = styled(Box)`
  background-color: gray;
  color: green;
`;
```

### animation keyFrames

- 애니메이션을 컴포넌트 안에 직접 작성해도 되지만, `styled-components`의 `keyframes`를 활용하여 애니메이션을 작성할 수 있다.

```jsx
const moveAnimation = keyframes`
    25%{
        transform: translateX(25%);
    }50%{
        transform: translateX(50%);
    }100%{
        transform: translateX(25%);
    }
`;

const AnimationBox = styled.div`
  width: 100px;
  height: 100px;
  background-color: black;
  animation: ${moveAnimation} 1s infinite;
`;
```

### Theme 적용하기

- 다크모드처럼 테마 자체를 적용하여 색상을 바꿀 수 있다.

```jsx
const darkTheme = {
  textColor: "white",
  backgroundColor: "black",
};

const lightTheme = {
  textColor: "black",
  backgroundColor: "white",
};

ReactDOM.render(
  <React.StrictMode>
    <ThemeProvider Theme={darkTheme}>
      <App />
    </ThemeProvider>
  </React.StrictMode>,
  document.getElementById("root")
);

const Title = styled.div`
  color: ${(props) => props.theme.textColor};
`;

const Notice = styled.div`
  background-color: ${(props) => props.theme.backgroundColor};
`;
```

### 공통 css

- CSS 초기화 또는 통일화를 위해 reset.css나 normalize.css를 쓰곤 하는데 스타일드 컴포넌트에서도 관련 패키지를 제공하고 있다.
  - reset.css => styled-reset
  - normalize.css => styled-normalize
- 스타일드 컴포넌트에서 제공하는 `{createGlobalStyle}`을 사용하여 reset이나 normalize를 불러오고 사용할 수 있다.

```jsx
import { createGlobalStyle } from "styled-components";
import Normalize from "styled-normalize";

const GlobalStyle = createGlobalStyle`
  ${Normalize};
 
  * {
    margin: 0;
    padding: 0;
  }
 
  body {
    background-color: #f0f0f0;
  }
`;

export default GlobalStyle;
```

```jsx
ReactDOM.render(
  <React.StrictMode>
    <GlobalStyle />
    <ThemeProvider theme={pastelTheme}>
      <App />
    </ThemeProvider>
  </React.StrictMode>,
  document.getElementById("root")
);
```

### 따라해보기

- `polished`를 설치하면 css를 더 자유롭게 사용할 수 있는 간편한 함수들을 제공한다.
- `props`를 컴포넌트로 내려 전달되는 컬러나 이벤트 등을 추가할 수 있다.

```jsx
const backColor = {
    kb: "yellow",
    sh: "blue",
    nh: "green"
}

const onEventAnimation = (color) => keyframes`
    30%, 70% {
      box-shadow: 0rem 0.2rem 1.2rem -0.1rem ${lighten(0.05, color)};
    }
`;

const BankButton = styled.button`
    // ...
    ${(props) => {
        consts color = backColor[props.name];

        const eventStyle = () => {
             if (props.event) {
                return css`
					grid-column: 1 / 4;
                    order: -1;
                    animation: ${onEventAnimation(color)} 3s infinite;
                `;
                }
        return css`
            background-color: ${color};

            &:hover{
                cursor: pointer;
                background-color: ${darken(0.05, color)};
            }

            &:disabled{
                color: #999;
                background-color: #c0c0c0;
                cursor: not-allowed;
            }

            ${eventStyle};
        `;
    }}
`
```

```jsx
function App() {
  return (
    <React.Fragment>
      <Bank name="kb">국민</Bank>
      <Bank name="sh">신한</Bank>
      <Bank name="nh">농협</Bank>
    </React.Fragment>
  );
}

function Bank({ children, ...reset }) {
  return <BankButton {...reset}>{children}</BankButton>;
}
```
