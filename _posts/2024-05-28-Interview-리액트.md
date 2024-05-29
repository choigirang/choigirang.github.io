---
layout: post
title: Interview 22장 - 리액트
author: admin
date: 2024-05-28 00:00:00 +900
lastmod: 2024-05-28 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [INTERVIEW]
tags: [react, state, effect]
---

# Interview

## React

- 페이스북에서 만든 자바스크립트 라이브러리로, 재사용성이 높은 컴포넌트 라는 단위를 기반으로한 개발이 가능하다.
- 데이터는 부모요소에서 자식요소로 단방향으로 흐른다는 특징을 갖고 있다.

### JSX

- 리액트에서 사용하는 문법으로 자바스크립트에 XML(데이터를 기술하는)이 합쳐진 문법으로, HTML 태그에 중괄호를 사용하거나 기존 HTML의 정규 속성 이외에도 추가적인 임의의 속성을 선언하여 사용이 가능하다.

### 제어 컴포넌트 vs 비제어 컴포넌트

- 제어 컴포넌트란, `input value`에 대한 값이, 사용자가 입력할 때마다 React의 state를 통해 제어되는 것을 말한다.(onChange => e.target.value 가 변할 때마다 렌더링)
- 비제어 컴포넌트란, `useRef`를 사용하여 사용자가 입력한 `input value`에 대해 즉각적인 상태 변화를 감지하지 않고, 핸들러를 통해 데이터를 전송할 때 `input ref`를 활용하여 입력된 값만 가져온다.

### SPA (single page application)

- 한 페이지 안에서 필요한 정보에 따라 부분적으로 업데이트 하는 애플리케이션.

### 가상 돔 (Virtual DOM)

- 일반적으로 브라우저는 `HTML 파싱 => DOM 객체 생성 => CSS 파싱 => 스타일 규칙 생성`이 이루어진다.
- 이 과정을 합하여 브라우저에 보여주어야 하는 `render tree`를 형성하고, 레이아웃과 페인트 작업을 한다.
- 각각의 객체(DOM)는 데이터가 업데이트 되거나 이벤트가 발생하면 위의 과정을 처음부터 다시 그리게 되는데, 이러한 불필요한 렌더링을 줄이기 위한 가상의 DOM이다.
- DOM이 그려진 후 데이터 입력이나 이벤트가 발생하여 업데이트 되어야 하는 경우, 가상 돔이 실제 돔과 변화를 비교하여 필요한 부분만 부분적으로 업데이트 하여 불필요한 렌더링을 방지한다.

#### hooks

##### useState

- 컴포넌트의 상태를 관리, 상태에 따른 즉각적인 리렌더링

##### useEffect

- 렌더링 이후 실행할 코드를 만들고, 사이드 이펙트를 관리할 의존성 배열의 값이 변경되면 실행할 콜백함수를 지정한다.

**class**

- componentDidMount : 컴포넌트를 만들고 첫 렌더링 완료 후 실행
- componentDidUpdate : 리렌더링이 완료될 때마다 실행
- compoenntWillUnMount : 컴포넌트를 언마운트 할 때 실행

##### useRef

- 컴포넌트나 HTML의 DOM요소를 관리할 때 사용

##### useMemo & useCallback

- 성능 최적화를 위한 훅으로, 함수가 렌더링 때마다 새롭게 추가되는 것을 방지하고 의존성 배열의 값이 변경될 때만 실행한다.
