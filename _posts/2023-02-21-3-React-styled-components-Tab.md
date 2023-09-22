---
layout: post
title: React 14장 - Tab 버튼
author: admin
date: 2023-02-21 00:00:00 +900
lastmod: 2023-02-21 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT] # 대문자로 작성
tags: [react, components, jsx, styled-components, tab]
---

# React

## Tab

- 클릭 시 버튼이 이동하는 `Tab`버튼 만들기

### 컴포넌트 짜기

- 컴포넌트를 나눈다.
- 우리가 작성해야 하는 것은 탭 버튼을 감싸고 있는 박스 하나.
- 아래 보여줄 텍스트 박스 하나.

```jsx
return (
    <TabContainer>
        <li></li>
    </TabContainer>
    <Desc>
        <p></p>
    <Desc>
)
```

### Styled-Components

- TabContainer 컴포넌트는 버튼의 배경과 버튼 자체를 갖고 있다.
- TabContainer 컴포넌트의 `li`에 `class`를 지정하여 값을 넣어주고, 탭의 상태값에 따라 클래스를 넣고 빼줄 거기 때문에, 만약 상태값이 어떠한 값을 가지면 임의의 클래스를 넣어주어 속성값이 들어가게 하고, 그렇지 않으면 클래스를 빼서 속성값이 빠지게 한다.
- `styled.ul`을 지정하여 TabContainer 컴포넌트 안을 `ul`이 감싸고 있게 한다.
- 기본 클래스인 `submenu`, 선택했을 시 들어갈 효과인 `focused`를 작성한다.
- 탭 메뉴를 선택 시, 보여줄 텍스트에 대한 스타일을 작성한다.

```jsx
const TabContainer = styled.ul`
  background-color: #dcdcdc;
  color: rgba(73, 73, 73, 0.5);
  font-weight: bold;
  display: flex;
  flex-direction: row;
  justify-items: center;
  align-items: center;
  margin-bottom: 7rem;

  > .submenu {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 3rem;
    width: 32%;
    margin-right: 2%;
  }

  .focused {
    background-color: white;
  }

  & div.desc {
    text-align: center;
  }
`;

const Desc = styled.div`
  text-align: center;
`;
```

### 기능 구현

#### 상태값 작성

- 탭 버튼을 클릭했을 때, 선택한 탭 버튼의 `index`에 따른 상태값이 변화하게 한다.
- 탭 버튼을 클릭했을 때, 보여줄 정보를 갖고 있게 한다.
- 탭 버튼을 클릭했을 때, 상태값을 변경시켜 줄 함수를 작성한다.

```jsx
const [currentTab, setCurrentTab] = useState(0);

const menu = [
  { name: "Tab1", content: "Tab menu One" },
  { name: "Tab2", content: "Tab menu Two" },
  { name: "Tab3", content: "Tab menu Three" },
];

const selectMenu = (index) => {
  setCurrentTab(index);
};
```

#### 컴포넌트에 이벤트 핸들러 부여

- TabContainer안의 `li`를 클릭했을 때 `selectMenu`함수를 실행시켜 `currentTab`의 값이 바뀌게 한다.
- 바뀐 값에 따라 클래스에 `focused`가 추가되어 색상이 바뀌게 만든다.
- `map()`에 의해 전달되는 두 번째 인자인 `idx`는 `menu`가 가진 `index`를 뜻한다.
- `li`를 클릭 시, `selectMenu`함수가 실행되며 각각의 `li`가 가진 `idx` 값으로 `currentTab`의 `index` 가 바뀌게 된다.
- 바뀐 `currentTab`의 `content`를 Desc 컴포넌트에 출력한다.

```jsx
return (
  <>
    <TabContainer>
      {menu.map((el, idx) => {
        return (
          <li
            key={idx}
            onClick={() => selectMenu(idx)}
            className={`${currentTab === idx ? "submenu focused" : "submenu"}`}
          >
            {el.name}
          </li>
        );
      })}
    </TabContainer>
    <Desc>{menu[currentTab].content}</Desc>
  </>
);
```

### stories 작성

- 컴포넌트명과 `stories.js` 를 작성하면 컴포넌트의 `stories`로 인식한다.
- Tab 컴포넌트를 불러온다.
- 기본값의 `title`은 `Example`의 폴더 안에 Tab 컴포넌트를 의미한다.
- 기본값의 `component`는 Tab 컴포넌트를 지칭한다.
- `stories`는 storybook
- [stories 설명보기](https://choigirang.github.io/posts/3-React-Storybook/){:target="\_blank"}

```jsx
import React from "react";
import { Tab } from "./components/Tab.js";

export default {
  title: "Example/Tab",
  component: Tab,
};

const Template = (args) => <Tab {...args} />;

export const Primary = Template.bind({});
Primary.args = {
  primary: true,
  label: "Tab",
};
```

#### 코드

```jsx
import { useState } from "react";
import styled from "styled-components";

const TabContainer = styled.ul`
  background-color: #dcdcdc;
  color: rgba(73, 73, 73, 0.5);
  font-weight: bold;
  display: flex;
  flex-direction: row;
  justify-items: center;
  align-items: center;
  margin-bottom: 7rem;

  > .submenu {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 3rem;
    width: 32%;
    margin-right: 2%;
  }

  .focused {
    background-color: white;
  }

  & div.desc {
    text-align: center;
  }
`;

const Desc = styled.div`
  text-align: center;
`;

export const Tab = () => {
  const [currentTab, setCurrentTab] = useState(0);

  const menu = [
    { name: "Tab1", content: "Tab menu One" },
    { name: "Tab2", content: "Tab menu Two" },
    { name: "Tab3", content: "Tab menu Three" },
  ];

  const selectMenu = (index) => {
    setCurrentTab(index);
  };

  return (
    <>
      <TabContainer>
        {menu.map((el, idx) => {
          return (
            <li
              key={idx}
              onClick={() => selectMenu(idx)}
              className={`${
                currentTab === idx ? "submenu focused" : "submenu"
              }`}
            >
              {el.name}
            </li>
          );
        })}
      </TabContainer>
      <Desc>{menu[currentTab].content}</Desc>
    </>
  );
};
```
