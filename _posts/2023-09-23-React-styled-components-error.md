---
layout: post
title: React 53장 - styled-components error
author: admin
date: 2023-09-23 00:00:00 +900
lastmod: 2023-10-02  00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, styled-componets, error, props, typescript]
---

# React

## props error

- 스타일 컴포넌트를 사용하는 와중에 처음 보는 에러가 발생했다.
- 전달되는 props에 따라 스타일을 변경해주려 하는데, props에 관한 에러였다.
  <img width="544" alt="image" src="https://github.com/choigirang/choigirang.github.io/assets/118104644/2885dd1d-f3e9-45f6-8267-c7510158b894">

- 이러한 에러가 발생한 곳에 대한 예제는 아래와 같다.

```jsx
// 타입 지정
type StackProps = {
  stack: boolean,
};

// 현재 스택
const [stack, setStack] = useState < boolean > false;

// 선택한 스택
const selectedStack = useSelector((state: RootState) => state.stack.stack);

return <SelectItem stack={stack === seletedStack} />;

// 스타일
const SelectItem =
  styled.div <
  StackProps >
  `
    background-color: ${(props) => (props.stack ? "black" : "white")};
`;
```

- 많이 생략됐기 때문에 부연 설명을 하자면, 언어 기술을 가진 객체를 매핑하여 내가 선택한 스택과 매핑하여 펼쳐진 스택이 일치하는 경우 배경색을 바꿔주고자 한다.
  - **그냥 클릭한 div의 배경색을 바꿔주려고 한다.**
- 이렇게 `boolean`값을 사용하여 배경색을 바꿔주고자 하였는데 위와 같은 에러는 처음 보는 에러였다.
- 이전에 스타일 컴포넌트를 사용하면서 위와 같이 참,거짓 값을 이용하여 값을 바꿔주었을 때는 아무런 이상이 없었다.
- 연산자의 문제인가 싶어 그냥 `false`값을 임의로 부여해보았는데 그래도 같은 에러가 발생했다.

[styled-components DOM boolean에러](https://jakemccambley.medium.com/transient-props-in-styled-components-3105f16cb91f)

- 위의 자료에 나와있는 설명을 요약해보자면, **DOM에서는 boolean값을 전달할 때 요소의 속성으로 사용될 수 있다는 에러이다.**
- 무슨 얘기인지 아래의 예시 코드가 있다.

### 예시 코드

```jsx
<InputElement bg={background}/>

<input class="InputElement" bg="false"/>
```

- 위의 예시처럼, 스타일 컴포넌트로 `bg`라는 boolean값에 따라 배경색을 변경하는 코드를 작성했다고 가정한다.
- 이러한 컴포넌트는 브라우저에서 렌더링 될 때, `bg`라는 속성으로 렌더링되는데 `input`태그에는 `bg`라는 속성이 없는 것이 당연하다.
- 그렇기 때문에 에러가 발생하는 것이고, `$`기호를 사용하여 `이 속성은 스타일 컴포넌트에서만 사용할 거야~`를 미리 알려주어 해결할 수 있다.
  - 이러한 방법을 **prefix**라고 하며, 성능 최적화, 스타일 가독성 및 유지 보수성, 코드 충돌 방지 등의 여러 이점을 가진다.
- 또는 **DOM으로 전달되지 않는 props명으로 대체**하여도 된다.
  - **bg={} => isBg={}**

참고자료 : https://velog.io/@jiwonyyy/Styled-components%EC%97%90%EC%84%9C-props-%EB%98%91%EB%98%91%ED%95%98%EA%B2%8C-%EB%84%98%EA%B2%A8%EC%A3%BC%EA%B8%B0-feat.-Received-true-for-a-non-boolean-attribute
