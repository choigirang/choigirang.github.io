---
layout: post
title: React 49장 - rendering, re-render
author: admin
date: 2023-06-27 00:00:00 +900
lastmod: 2023-06-27 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, rendering]
---

# React

- `useMemo`의 리렌더링에 대한 정보들을 들여다보다가, 아직 리액트의 기본적인 특성들을 깊이 이해하지 못 했다는 생각이 들어 작성해보는 글이다.

## rendering

- 리액트에서 렌더링이란, 리액트를 설치하면 볼 수 있는 `ReactDOM.render()` 함수가 그것이다.
- 또한, 초기에 컴포넌트의 정보를 토대로 화면이 어떻게 구성될지를 결정하고, 보여질 수 있도록 화면을 구성하는 것이 **렌더링**이다.

## re-render

- 리렌더링은 초기에 구성된 화면에 대한 내용에서, 상태값이 업데이트 되거나 어떠한 컴포넌트에 대해 연결되어 있는 컴포넌트들에 대해 새롭게 구성하는 것이다.
- 리렌더링 과정을 거치면 컴포넌트가 새롭게 갈아끼워지는 것인데,이러한 과정에서 초기에 구성했던(렌더링된) 화면과 변경을 감지하고 새롭게 구성될(리렌더링) 화면과의 비교를 통해, 변화가 있는 요소들을 DOM에 반영하는 것이다.
- 리렌더링은 어떠한 값을 사용하고 있는 여러 개의 컴포넌트가 존재할 때, 그 값이 변경된다면 그 값을 반영한 모든 컴포넌트들이 전부 재구성된다.
- 이렇게 잦은 렌더링 과정이 일어날 경우, 메모리 측면에서 애플리케이션 성능에 문제가 일어날 수밖에 없기 떄문에 최적화가 필요하다.

### re-render의 조건

- 리렌더링이 일어나기에는 몇 가지 조건이 있다.
- 자식 컴포넌트의 state나 props에 변경사항이 있는 것과는 무관하게, 부모 컴포넌트가 리렌더링되면 자식 컴포넌트도 리렌더링 된다.
- 부모/자식, 형제 관계를 주의해야 하는데 관련 자료를 읽다가 매우 복잡하고도 의아한 자료를 발견했다.
- [참고자료](https://velog.io/@mogulist/understanding-react-rerender-easily)
- 짧게 요약을 하자면, 부모/자식 관계로 보여지는 컴포넌트가 리렌더링의 관점에서는 아닐 수도 있기 때문에 컴포넌트의 구조만으로 부모/자식 관계를 생각하여 `"A 컴포넌트가 변하면 당연히 B 컴포넌트도 변하겠지?"`라고 생각했지만, 사실은 영향을 받지 않을 수도 있다.

```jsx
function App() {
  return (
    <Parent lastChild={<ChildC />}>
      <ChildB />
    </Parent>
  );
}

function Parent({ children, lastChild }) {
  return (
    <div className="parent">
      <ChildA />
      {children}
      {lastChild}
    </div>
  );
}
```

- `ChildB` 컴포넌트가 `Parent` 컴포넌트에 감싸져 있으므로, `Parent` 컴포넌트가 리렌더링되면 리렌더링되는 컴포넌트는 `ChildA`컴포넌트밖에 없다.
- **자식 컴포넌트란, 부모 컴포넌트의 JSX안에 사용된 모든 컴포넌트**, 로 정의되기 때문에 `ChildB`와 `ChildC`는 정확히 `App`컴포넌트의 자식들이다.
- `React DevTools`에서도 트리 구조를 확인할 수 있는데, 얼핏 보기엔 `Parent` 컴포넌트의 자식 컴포넌트라고 볼 수 있지만 `rendered by`를 확인하면 관계된 컴포넌트가 무엇인지 확인할 수 있다.
  ![image](https://velog.velcdn.com/images/mogulist/post/7f982312-305a-4670-aa17-ef0f6da37c37/image.png){: .center}
  ![image](https://velog.velcdn.com/images/mogulist/post/888d004a-f0c3-438c-b5c2-2547bfbded0c/image.png){: .center}

## 화면을 구성할 Virtual DOM과 DOM

- _"DOM은 HTML, XML document와 상호작용하고 표현하는 API이다. DOM은 browser에서 로드되며, Node(이하 노드) 트리(각 노드는 document의 부분을 나타낸다)로 표현하는 document 모델이다. (ex. element, 문자열, 혹은 코멘트)"_
- Document Object Model의 약자로, html,xml,css 등을 트리 구조로 인식하고, 데이터를 객체로 간주하고 관리한다.
  - Node 트리
  - ![image](https://github.com/choigirang/none.github.io/assets/118104644/20584bcb-0102-4f7a-b01b-98cb6bbf9268)

### Node

- 트리 구조에서 데이터의 상하위 계층을 나타내는 항목(연결점, 접점)이며 기초 단위를 말한다.
- DOM을 완벽히 이해하기 위한 Node, 노드 유형은 아래와 같다.
  - `console`에 `Node`를 치면 유형들을 확인할 수 있다.
  - DOCUMENT_NODE : `window, document`
  - ELEMENT_NODE : `<html>,<body>,<a>,<p>,<script>,<style>,<h1>`
  - ATTRIBUTE_NODE : `class=h1`
  - TEXT_NODE : 줄바꿈, 공백을 포함한 HTML 문서 내의 텍스트
  - DOCUMENT_FRAGMENT_NODE : `document.createDocumentFragment()`
  - DOCUMENT_TYPE_NODE : `<!DOCTYPE html>`
- 작성된 HTML 문서가 브라우저에 의해 해석되어 실제 문서를 나타내는 노드 트리가 되며, 내가 작성한 HTML 문서는 단순한 문자열일 뿐이며, 브라우저가 이해하기 위해 노드로 변환해야 한다.

## 다시 DOM

- 즉, **DOM은 HTML과 자바스크립트를 이어주는 공간으로, 내가 작성한 HTML을 자바스크립트가 이해할 수 있도록 객체로 변환하는 것이다.**
- DOM 트리 구조와 같은 구조체는 Virtual DOM을 가지고 있다.
- 가상 DOM은 이벤트가 발생할 때마다 가상 DOM을 만들어, 실제 DOM과 비교하고 변경이 필요한 최소의 변경사항만을 실제 DOM에 반영해 앱의 효율성과 속도를 개선하는 것이다.

### 브라우저 동작 원리

![image](https://github.com/choigirang/none.github.io/assets/118104644/b1f81d73-5385-440f-8573-0f437421be4c)

1. 브라우저가 HTML을 전달받으면 이를 변환(파싱)하고 노드들로 이루어진 DOM 트리를 만든다.
2. 외부의 CSS 파일과 각 노드들의 스타일을 파싱하여 스타일을 입힌 Render 트리를 만든다.
3. Render 트리가 만들어지면, 각 노드들이 화면에서 어디에 나타나야 하는지에 대한 위치가 주어진다.
4. `paint()` 메서드를 호출하여 내가 구현하고 싶은 화면이 출력된다.

- 오타 수정이나, 이미지 첨부 등에 대한 사소한 작업에도 위와 같은 작업들이 처음부터 다시 시작한다.
- 사이트의 페이지가 늘어나며, 사소한 작업에 전체 사이트를 처음부터 렌더링 해야 하는 과정이 반복되기 때문에 매우 비효율적인 작업이 됐고 이를 보완하기 위한 `Virtual DOM`이 등장했다.

### Virtual DOM

![image](https://github.com/choigirang/none.github.io/assets/118104644/0653f086-9080-4305-8223-b88885362255)
![image](https://github.com/choigirang/none.github.io/assets/118104644/1f28c6f9-b656-443f-9c56-203d4b557b42)

- 가상 DOM이 실제 DOM과 비교하여 변경된 부분만을 수정하는데, 만약 가상 DOM이 없었다면 모든 동그라미가 빨간색으로 바뀌었을 것이다.
- `직접 DOM에 접근하는 것은 지양해야 한다.` 이는 DOM에 직접 접근해도 문제가 되진 않지만, DOM이 직접 변경된다면 사소한 변경 사항에도 전체가 리렌더링 되기 때문에 브라우저에 과부하가 올 수 있다.
