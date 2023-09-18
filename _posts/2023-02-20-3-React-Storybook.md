---
layout: post
title: React 10장 - Storybook
author: admin
date: 2023-02-20 00:00:00 +900
lastmod: 2023-02-20 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]  # 대문자로 작성
tags: [react, components, storybook]
---
# Storybook
- CDD(Component Driven Development)를 하기 위한 도구로, 각각의 컴포넌트들을 따로 볼 수 있게 구성해 주어 한 번에 하나의 컴포넌트에서 작업할 수 있다.
- 복잡한 개발 스택을 시작하거나, 특정 데이터를 데이터베이스로 강제 이동하거나, 애플리케이션을 탐색할 필요 없이 전체 UI를 한눈에 보고 개발할 수 있다.
- 재사용성 확대를 위해 컴포넌트를 문서화하고, 자동으로 컴포넌트를 시각화하여 시뮬레이션할 수 있는 다양한 테스트 상태를 확인할 수 있다.
- 이를 통해 버그를 사전에 방지하고 테스트 및 개발 속도를 향상시키는 장점이 있으며 의존성을 걱정하지 않고 빌드할 수 있다.


## Storybook 주요 기능
- UI 컴포넌트들을 카탈로그화하기
- 컴포넌트 변화를 Stories로 저장하기
- 핫 모듈 재 로딩과 같은 개발 툴 경험 제공
- 리액트를 포함한 다양한 뷰 레이어 지원


## 사용방법
- 본인이 만든 프로젝트의 폴더 안에서 Storybook을 설치한다.
  - `npx storybook init`
  - 꼭 리액트가 아니더라도 프론트엔드 라이브러리에 맞는 사용 환경을 알아서 만들어주기 때문에 다양한 프론트엔드 라이브러리에서 사용할 수 있다.
- 설치가 완료되면 storybook 폴더가 생성된다.
  - `npm run storybook` 명령어를 통해 StoryBook을 실행시킬 수 있다.
  - 리액트가 `localhost:3000`으로 접근하듯이, `localhost:6006`으로 접근하여 실행시킨다.
- `stories` 폴더 안에 있는 Storybook에서 만들어놓은 예시 스토리를 확인할 수 있다.
  - 애플리케이션을 실행하고 이벤트를 통해 상태를 변경하는 과정을 거치지 않아도 상태 변화에 따른 컴포넌트 변화를 확인할 수 있다.


#### 예시
- Title 컴포넌트
  - 컴포넌트는 `title`과 `textColor`를 인자로 받는다.
  - 인자로 받은 `title`은 우리가 만들 요소의 이름으로 쓰이고 입력받은 `textColor`는 색상의 값으로 사용된다.



```jsx
import React from "react"

const Title = ({title, textColor}) => {

    return (
        {%raw%}
        <>
            <h1 title={{color: textColor}}>{title}</h1>
        </>
        {%endraw%}
    )
}

export default Title;
```


- Title.stories 컴포넌트
  - `./storybook` 안에 있는 Storybook 설정 파일에 의해 컴포넌트 파일과 똑같은 이름에 `.stories`를 붙여 파일을 만들면 알아서 스토리로 인식한다.
  - `export default`
    - Title 컴포넌트를 불러오고 기본값으로 등록을 해줄 건데, 무엇을 등록을 할 거냐
    - `title` : 컴포넌트의 이름을 하나의 카테고리에 넣어 카테고리화 시켜 줄 것이다.
    - `component` : 우리가 불러올 컴포넌트의 이름
    - `argTypes` : `title`과 `textColor`로 받는 타입은 `text`타입을 받아 이것을 각각 만들 요소에 `title`과 `textColor`로 사용하겠다.
  - Template 컴포넌트를 하나 만들어, Template 컴포넌트에서 받은 인자를 Title 컴포넌트의 `props`로 넘겨주겠다.
    - 하나의 문법으로 생각하면 된다.
  - RedTitle 이라는 컴포넌트를 생성한다.
    - 이 컴포넌트에는 `title`과 `color`에 대한 정보를 담고 있는데, 스토리북에 생성된 RedTitle을 클릭하면, RedTitle에 담겨있는 `title`과 `color` 정보가 Template 컴포넌트로 전달된다.
    - 전달된 값을 받아 Template 컴포넌트에서 Title의 `props`로 넘겨주어 Title 컴포넌트 파일에 작성된 요소를 생성한다.



```jsx
import Title from "./Title.js"

export default {
    title: "Practice/Title",
    component: Title,
    argTypes: {
        title : {control: "text"},
        textColor: {control: "text"}
    }
}

const Template = (args) => <Title {...args} />

export const RedTitle = Template.bind({})

RedTitle.args={
   title: "Red Title",
   textColor: "red"
}

export const BlueTitle = Template.bind({})

BlueTitle.args={
   title: "Blue Title",
   textColor: "blue"
}
```


- 전달인자로 직접 받는 스토리
  - 위에서 작성한 코드에 템플릿을 활용하지 않고 바로 전달인자로 받고 있는 예제이다.
  - 아래의 코드를 스토리 바로 밑에 작성한다.



```jsx
***
export const StorybookTitle = (args) => {
    return <Title {...args} />
}
```



#### 예시 2
- 위의 코드는 정해진 컴포넌트 RedTitle, BlueTitle과 같은 컴포넌트에 미리 값을 담아, 그 값을 Title 컴포넌트에게 전달해주는 방식이었다.
- 2번째 예시는 우리가 작성한 값을 입력받아 색상과 타이틀을 정하겠다는 방식으로 구현한 것이다.
- Button 컴포넌트



```jsx
import React from "react"
import styled from "styled-components"

const StyledButton = styled.button`
    background: ${(props) => props.color || "white"}
    width: ${(props) => (props.size === "big" ? "200px":"100px")}
    height: ${(props) => (props.size === "big" ? "80px":"40px")}
`

const Button = ({color, size, text}) => (
    <StyledButton color={color} size={size}>{text}</StyledButton>
)

export default Button
```



- Button.stories.js



```jsx
import Button from "./Button.js"

export default {
    title: "Practice/Button",
    component: Button,
    argTypes: {
        color: {control: "color"},
        size: {constrol: {type:"radio", options: ["big", "small"]}},
        text: {control: "text"}
    }
}

export const StorybookButton = (args) => (
    <Button {...args} />
)
```