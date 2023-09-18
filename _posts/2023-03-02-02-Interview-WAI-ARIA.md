---
layout: post
title: Interview 6장 - WAI-ARIA
author: admin
date: 2023-03-02 00:00:00 +900
lastmod: 2023-03-02 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [INTERVIEW] # 대문자로 작성
tags: [wai-aria]
---

# CS

## WAI-ARIA

- WAI와 ARIA가 합쳐진 단어이다.
  - **WAI**(Web Accesibility Initiative) : 웹 표준을 정하는 W3C에서 웹 접근성을 담당하는 기관
  - **ARIA**(Accessible Rich Internet Applications) : 장애가 있는 사람들이 웹 콘텐츠와 응용 프로그램을 더 쉽게 액세스할 수 있도록 하는 기술
    - **RIA**(Rich Internet Applications) : 따로 프로그램을 설치하지 않아도 웹 브라우저를 통해 사용할 수 있는 **웹 애플맄이션**, SPA를 의미하는 경우가 많다.

## WAI-ARIA의 필요성

- WAI-ARIA는 HTML 요소에 추가적으로 의미를 부여할 수 있게 해준다.
  - 시맨틱 요소만으로 의미를 충분히 부여할 수 없는 상황에, HTML요소에 추가적인 의미를 부여하여 더 원활한 페이지를 탐색 할 수 있게 해준다.
  - WAI-ARIA를 남용해선 안 되며, 시맨틱 HTML을 작성하는 것이 최우선이다.
  - SPA처럼 AJAX를 사용하는 상황, 새로고침 없이 페이지의 내용이 바뀌는 상황에도 변경된 영역에 대한 정보를 전달해주어 동적인 콘텐츠에서도 웹 접근성을 향상시킬 수 있다.

## 사용법

- **역할**(role) : HTML 요소의 역할을 정의하는 속성
- **상태**(state) : 요소의 현재 상태를 나타내는 속성
- **속성**(property) : 요소의 특징을 정의하는 속성

### 역할 role

- HTML요소 종류와 역할이 맞지 않을 때, 어떤 역할을 하는 요소인지 명시해줄 때 사용할 수 있는 속성이다.
- 버튼 역할을 하는 `div`요소를 사용했다면, 이 요소가 버튼 역할을 하고 있음을 표시해주어야 한다.
  ```html
  <div role="button">div이지만 button으로 사용되는 요소</div>
  ```
- 잘못된 예시 : HTML 요소로 충분히 파악할 수 있는 내용을 WAI-ARIA로 또 설명해 줄 필요는 없다.
  ```html
  <button role="button">button인 요소</button>
  ```
- 잘못된 예시 : 시맨틱 요소 본연의 의미를 임의로 바꾸지 않아야 한다.
  ```html
  <h1 role="button">h1인 요소</h1>
  ```

#### 예시

```html
<div>
  <li>Tab1</li>
  <li>Tab2</li>
  <li>Tab3</li>
</div>

<div>Tab menu One</div>
<div>Tab menu Two</div>
<div>Tab menu Three</div>
```

```html
<div role="tabList">
  <li role="tab">Tab1</li>
  <li role="tab">Tab2</li>
  <li role="tab">Tab3</li>
</div>

<div role="tabpanel">Tab menu One</div>
<div role="tabpanel">Tab menu Two</div>
<div role="tabpanel">Tab menu Three</div>
```

### 상태 state

- `aria-selected`

  - 여러 개의 탭이 있을 때, 어떤 탭이 선택되었는지 상태를 표시하기 위해 사용하는 속성이다.

  ```html
  <div role="tabList">
    <li role="tab" aria-selected="true">Tab1</li>
    <li role="tab" aria-selected="false">Tab2</li>
    <li role="tab" aria-selected="false">Tab3</li>
  </div>
  ```

### 속성 property

- `aria-label`

  - 요소에 라벨을 붙여주는 기능을 한다.
  - 예를 들어 돋보기 모양의 검색 버튼과 X모양이 닫기 버튼이 있을 때, HTML 구조만으로는 어떤 역할을 하는 버튼인지 파악할 수 없고, 라벨을 붙여주기 위해 사용한다.

  ```html
  <button><img src="X.png" /></button>
  <button><img src="돋보기.png" /></button>
  =>
  <button aria-label="닫기"><img src="X.png" /></button>
  <button aria-label="닫기"><img src="돋보기.png" /></button>
  ```

- `aria-live`
  - 해당 요소가 실시간으로 내용을 갱신하는 영역인지 표시한다.
  - `alert`,`modal`,`dialog`와 같은 역할을 하는 요소이거나, AJAX 기술을 사용하여 실시간으로 내용을 갱신하는 영역에 사용하는 속성이다.
    - `polite` : 스크린 리더가 현재 읽고있는 내용을 모두 읽고나서 갱신된 내용을 사용자에게 전달한다.
    - `assertive` : 스크린 리더가 현재 읽고있는 내용을 중단하고 갱신된 내용을 바로 사용자에게 전달한다.
