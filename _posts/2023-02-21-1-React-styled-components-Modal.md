---
layout: post
title: React 12장 - Modal 버튼
author: admin
date: 2023-02-21 00:00:00 +900
lastmod: 2023-02-21 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT] # 대문자로 작성
tags: [react, components, jsx, styled-components, modal]
---

# React

## Modal

- 클릭 시 알림창을 생성하는 `Modal` 버튼 만들기

### 컴포넌트 짜기

- 컴포넌트를 나눈다.
- ModalContainer : 전체를 포함하고 있는 컴포넌트 하나
- ModalBtn : 모달 기능을 구현할 버튼 하나
- ModalBackDrop : 검은색 배경을 구현할 컴포넌트 하나
- ModalView : 알림창을 나타낼 컴포넌트 하나

```jsx
return (
  <ModalContainer>
    <ModalBtn />
    <ModalBackDrop>
      <ModalView />
    </ModalBackDrop>
  </ModalContainer>
);
```

### Styled-Components

- ModalContainer

  - Modal 컴포넌트 안에 위치할 하위 컴포넌트들을 `display:flex`와 `justify-content:center`,`align-items:center`을 통해 가운데 정렬을 한다.
  - `position` 값은 하위 컴포넌트의 `position`의 기준이 되기위해 사용한다.

- ModalBtn

  - ModalContainer 안에 위치할 `button`인 ModalBtn 컴포넌트이다.
  - `display`,`justify-content`,`align-items` 동일

- ModalBackDrop

  - ModalBtn을 눌렀을 시 BackDrop과 View를 보여줄 컴포넌트이다.
  - `display`,`justify-content`,`align-items` 동일
  - 상위 컴포넌트인 ModalConatiner의 `position`을 기준으로 절대적 위치값을 설정한다.
  - `position:absolute`를 사용하지 않을 시, ModalBtn과 같은 하위 컴포넌트이기 때문에 ModalBtn에 옆에 위치하게 된다.
  - 따라서, `z-index` 값을 부여하여 ModalBtn보다 앞에 위치하게 하며 전체 화면을 덮을 수 있도록 ModalContainer의 넓이값과 높이값을 모두 받아온다.

- ModalView
  - ModalBackDrop 안에 위치할 알림창 컴포넌트이다.
  - ModalBackDrop의 하위 컴포넌트이기 때문에, ModalBackDrop의 `justify-content`,`align-items`에 의해 중앙 정렬된다.

```jsx
const ModalContainer = styled.div`
  background-color: lightblue;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 20rem;
  position: relative;
`;

const ModalBtn = styled.div`
  background-color: blue;
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 10px;
  cursor: grab;
`;

const ModalBackDrop = styled.div`
    background-color: rgba(0,0,0,0.5);
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%
    height: 100%
    position: absolute;
    left: 0;
    top: 0;
    z-index: 10;
`;

const ModalView = styled.div.attrs((props) => ({
  role: "dialog",
}))`
  background: white;
  display: felx;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 200px;
  height: 100px;
  > . closeBtn {
    cursor: pointer;
  }
`;
```

### 기능 구현

#### 상태값 작성

- ModalBtn을 눌렀을 때의 상태에 따라 ModalBackDrop과 하위 컴포넌트인 ModalView 컴포넌트가 나타났다,사라졌다를 반복한다.
- ModalBtn이 클릭됐을 때 상태값이에 따라 전원을 키고 끄듯이 상태값이 달라져야하며, 상태값을 바꿔주기 위한 함수 `onHandle`을 작성해준다.

```jsx
const [isModal, setModal] = useState(false);

const onHandle = () => {
  setModal(!isModal);
};
```

#### 컴포넌트에 이벤트 핸들러 부여

- ModalBtn을 클릭했을 때 `onHandle`함수를 실행시켜 `isModal`의 값이 바뀌게 한다.
- 바뀐 값에 따라 ModalBackDrop과 ModalView가 나타나게 하는 조건을 건다.

```jsx
return (
  <ModalContainer>
    <ModalBtn onClick={onHandle}>{!isModal ? "Open Modal" : "Opend"}</ModalBtn>
    {!isModal ? null : (
      <ModalBackDrop onClick={onHandle}>
        <ModalView>
          <div className="closeBtn">X</div>
          <div>알림창이 열렸습니다.</div>
        </ModalView>
      </ModalBackDrop>
    )}
  </ModalContainer>
);
```

#### 다듬기

- 위의 내용만 갖고도 컴포넌트와 기능 구현은 끝났다.
- 문제점은 ModalBackDrop 컴포넌트를 클릭했을 때 실행되는 `onHandle`함수를, 하위 컴포넌트인 ModalView 컴포넌트가 그대로 상속받아 ModalView의 어느 영역을 클릭해도 `onHandle`함수가 실행된다는 것이다.
  - 이를 위해 `stopPropagation`메서드를 사용한다.
  - `event.preventDefault()`와 비슷한 의미를 가진 메서드로, 클릭이벤트가 발생했을 때, 이벤트가 전파되는 것을 막는 기능을 한다.
  - 사용자가 웹페이지 내의 버튼을 클릭했을 때, 버튼을 감싸고 있는 부모 태그들 또한 클릭 이벤트에 반응하게 되는데, 이것을 `Bubble Up`이라고 한다.
  - `event.stopPropagation()`을 사용하여 `Bubble Up`현상을 막는다.

```jsx
return (
  <ModalContainer>
    <ModalBtn onClick={onHandle}>{!isModal ? "Open Modal" : "Opend"}</ModalBtn>
    {!isModal ? null : (
      <ModalBackDrop onClick={onHandle}>
        <ModalView>
          <div
            className="closeBtn"
            onClick={(event) => {
              event.stopPropagation();
            }}
          >
            X
          </div>
          <div>알림창이 열렸습니다.</div>
        </ModalView>
      </ModalBackDrop>
    )}
  </ModalContainer>
);
```

### stories 작성

- 컴포넌트명과 `stories.js` 를 작성하면 컴포넌트의 `stories`로 인식한다.
- Modal 컴포넌트를 불러온다.
- 기본값의 `title`은 `Example`의 폴더 안에 Modal 컴포넌트를 의미한다.
- 기본값의 `component`는 Modal 컴포넌트를 지칭한다.
- `stories`는 storybook
- [stories 설명보기](https://choigirang.github.io/posts/3-React-Storybook/){:target="\_blank"}

```jsx
import React from "react";
import { Modal } from "./components/Modal.js";

export default {
  title: "Example/Modal",
  component: Modal,
};

const Template = (args) => <Modal {...args} />;

export const Primary = Template.bind({});
Primary.args = {
  primary: true,
  label: "Modal",
};
```

### 코드

```jsx
import { useState } from "react";
import styled from "styled-components";

const ModalContainer = styled.div`
  background-color: lightblue;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 20rem;
  position: relative;
`;

const ModalBtn = styled.div`
  background-color: blue;
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 10px;
  cursor: grab;
`;

const ModalBackDrop = styled.div`
    background-color: rgba(0,0,0,0.5);
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%
    height: 100%
    position: absolute;
    left: 0;
    top: 0;
    z-index: 10;
`;

const ModalView = styled.div.attrs((props) => ({
  role: "dialog",
}))`
  background: white;
  display: felx;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 200px;
  height: 100px;
  > . closeBtn {
    cursor: pointer;
  }
`;

const [isModal, setModal] = useState(false);

const onHandle = () => {
  setModal(!isModal);
};

return (
  <ModalContainer>
    <ModalBtn onClick={onHandle}>{!isModal ? "Open Modal" : "Opend"}</ModalBtn>
    {!isModal ? null : (
      <ModalBackDrop onClick={onHandle}>
        <ModalView>
          <div
            className="closeBtn"
            onClick={(event) => {
              event.stopPropagation();
            }}
          >
            X
          </div>
          <div>알림창이 열렸습니다.</div>
        </ModalView>
      </ModalBackDrop>
    )}
  </ModalContainer>
);
```
