---
layout: post
title: React 13장 - Toggle 버튼
author: admin
date: 2023-02-21 00:00:00 +900
lastmod: 2023-02-21 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT] # 대문자로 작성
tags: [react, components, jsx, styled-components, toggle]
---

# React

## Toggle

- 클릭 시 버튼이 이동하는 `Toggle`버튼 만들기

### 컴포넌트 짜기

- 컴포넌트를 나눈다.
- 우리가 작성해야 하는 것은 토글버튼을 감싸고 있는 박스 하나.
- 그 안에 이동할 작은 동그라미 버튼 하나.
- 토글의 상태에 따라 텍스트를 출력할 컴포넌트 하나이다.

```jsx
return (
    <ToggleContainer>
        <div></div>
        <div></div>
    </ToggleContainer>
    <Desc />
)
```

### Styled-Components

- Toggle 컴포넌트는 버튼의 배경과 버튼 자체를 갖고 있다.
- Toggle 컴포넌트의 `class`를 지정하여 값을 넣어주고, 토글의 상태값에 따라 클래스를 넣고 빼줄 거기 때문에, 만약 상태값이 어떠한 값을 가지면 임의의 클래스를 넣어주어 속성값이 들어가게 하고, 그렇지 않으면 클래스를 빼서 속성값이 빠지게 한다.
- `&`표시는 가상 선택자를 의미하며 현재의 요소를 뜻한다.
  - `hover`는 마우스가 올라갔을 때
  - `.class`는 어떠한 클래스가 들어갔을 때
- `position`을 상위 컴포넌트인 ToggleContainer에 주어 틀을 기준으로 `circle`을 조절한다.
- `Desc`는 우리가 입력할 텍스트의 위치를 잡는다.

```jsx
const ToggleContainer = styled.div`
    position: relative;
    margin-top: 8rem;
    left: 47%
    cursor: pointer;

    > .toggle-container {
        width: 50px;
        height: 24px;
        border-radius: 30px;
        background-color: gray;
        transition: 1s;
        & .toggle--checked {
            background: blue;
        }
    }

    > .toggle-circle {
        width: 22px;
        heigth: 22px;
        border-radius: 50%;
        background-color: #ffffff;
        position: absolute;
        transition: 1s;
        & .toggle--checked {
            left: 27px;
        }
    }
`;

const Desc = styled.div`
  display: flex;
  justify-content: center;
  margin-top: 1rem;
`;
```

### 기능 구현

#### 상태값 작성

- ToggleContainer을 눌렀을 때의 상태에 따라 `toggle--checked`클래스를 추가해준다.
  - `toggle--checked`가 추가되면 `toggle-container`의 색상을 바꿔주고,`toggle-circle`의
- ToggleContainer이 클릭됐을 때 상태값이에 따라 전원을 키고 끄듯이 상태값이 달라져야하며, 상태값을 바꿔주기 위한 함수 `toggleHandle`을 작성해준다.

```jsx
const [isOn, setIsOn] = useState(false);

const toggleHandle = () => {
  setIsOn(!isOn);
};
```

#### 컴포넌트에 이벤트 핸들러 부여

- ToggleContainer을 클릭했을 때 `toggleHandle`함수를 실행시켜 `isOn`의 값이 바뀌게 한다.
- 바뀐 값에 따라 `toggle-container`와 `toggle-circle`의 배경색과 버튼이 이동하는 조건을 작성해준다.

```jsx
return (
  <>
    <ToggleContainer onClick={toggleHandle}>
      <div className={`toggle-container ${isOn ? "toggle--checked" : ""}`} />
      <div className={`toggle-circle ${isOn ? "toggle--checked" : ""}`} />
    </ToggleContainer>
    <Desc>{isOn ? "Toggle Switch On" : "Toggle Switch OFF"}</Desc>
  </>
);
```

### stories 작성

- 컴포넌트명과 `stories.js` 를 작성하면 컴포넌트의 `stories`로 인식한다.
- Toggle 컴포넌트를 불러온다.
- 기본값의 `title`은 `Example`의 폴더 안에 Toggle 컴포넌트를 의미한다.
- 기본값의 `component`는 Toggle 컴포넌트를 지칭한다.
- `stories`는 storybook
- [stories 설명보기](https://choigirang.github.io/posts/3-React-Storybook/){:target="\_blank"}

```jsx
import React from "react";
import { Toggle } from "./components/Toggle.js";

export default {
  title: "Example/Toggle",
  component: Modal,
};

const Template = (args) => <Modal {...args} />;

export const Primary = Template.bind({});
Primary.args = {
  primary: true,
  label: "Toggle",
};
```

### 코드

```jsx
import { useState } from "react";
import styled from "styled-components";

export const ToggleContainer = styled.div`
    position: relative;
    margin-top: 8rem;
    left: 47%
    cursor: pointer;

    > .toggle-container {
        width: 50px;
        height: 24px;
        border-radius: 30px;
        background-color: gray;
        transition: 1s;
        & .toggle--checked {
            background: blue;
        }
    }

    > .toggle-circle {
        width: 22px;
        heigth: 22px;
        border-radius: 50%;
        background-color: #ffffff;
        position: absolute;
        transition: 1s;
        & .toggle--checked {
            left: 27px;
        }
    }
`;

const Desc = styled.div`
  display: flex;
  justify-content: center;
  margin-top: 1rem;
`;

export const Toggle = () => {
  const [isOn, setIsOn] = useState(false);

  const toggleHandle = () => {
    setIsOn(!isOn);
  };

  return (
    <>
      <ToggleContainer onClick={toggleHandle}>
        <div className={`toggle-container ${isOn ? "toggle--checked" : ""}`} />
        <div className={`toggle-circle ${isOn ? "toggle--checked" : ""}`} />
      </ToggleContainer>
      <Desc>{isOn ? "Toggle Switch On" : "Toggle Switch OFF"}</Desc>
    </>
  );
};
```
