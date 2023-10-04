---
layout: post
title: React 54장 - Toast Editor (1) 리액트 설치
author: admin
date: 2023-10-04 00:00:00 +900
lastmod: 2023-10-04  00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, editor, toast, draft, react17, react18, dom]
---

# React

## Editor

- 서버에 데이터를 전송할 에디터를 만들어보려고 한다.
- 기존에 사용하던 `draft.js`를 만들어 놓은 게 있지만, 이번 미니 프로젝트에서는 마크다운 형식의 게시글을 보여주고, 주로 마크다운 형식의 코드를 사용하기에는 적절하지 않았다.
- 주로 사용되는 에디터에는 대표적으로 `draft.js , toast ui, quill` 3가지가 있다.
- 각 각 갖고 있는 특성이 있는데, 마크다운 에디터로 사용이 가장 간편한 `toast ui`의 사용 필요성이 느껴졌다.

## Toast Ui 설치

- 시작부터 쉽지 않았다.
- 리액트의 최신 18버전과 `Toast Ui`가 호환되지 않아 에러가 발생한다.
- `Toast Ui`는 17버전을 기반으로 하기 때문에 18버전에서 설치를 시도하면 아래와 같은 에러를 뱉어내는데, 에러를 내용을 살펴보면 리액트 버전에 관한 에러이다.

  - ![image](https://github.com/choigirang/choigirang.github.io/assets/118104644/9b7ff190-fb9d-4a47-8119-eeaf55f9ef60)

- 여기서 두 가지 방법이 존재한다.
  - 첫 번째는, `npm install --force`를 통해 충돌을 우회하여 설치하는 방법
  - 두 번째는, `npm install --legacy-peer-deps`를 통해 충돌을 무시하고 설치하는 방법

### --force와 --legacy

- `--force` 진행 시, `package-lock.json`에 몇 가지의 다른 의존 버전들을 추가하여 설치하기 때문에 완전하지 않다.
  - 그냥 안 맞는 부품 몇 가지를 끼워 맞추기 식으로 설치한다고 생각하면 된다.
- `--legacy` 진행 시, 일단 설치를 하겠다는 것인데, 이 같은 방법보다 `--force`를 더 선호한다고 한다.
- **둘 다 완전하게 안전한 방법은 없다.**
- 따라서, 다른 차선책인 `Toast UI`에서 요구하는 리액트 버전인 17버전으로 다운그레이드 하여 사용하려고 한다.

### 리액트 다운그레이드 18 => 17

1. 우선 설치된 `node_modules`와 `package-lock.json`가 충돌이 나기 때문에, 깨끗하게 밀어준다.

- `rm -rf node_modules package-lock.json`
- 리액트 프로젝트를 설치했을 때, `testing-library`가 함께 설치되어 있는데, 이놈이 계속 버전 충돌을 발생시킨다.
- `testing-library`에 관해 찾아봤는데 사용하지 않을 뿐더러 굳이 필요하지 않다고 판단되어 함께 깨끗히 밀어줬다.
  - `npm uninstall @testing-library/react @testing-library/jest-dom`
- [테스트 라이브러리에 관한 내용](https://tecoble.techcourse.co.kr/post/2021-10-22-react-testing-library/)

2. 그 다음 리액트 버전을 낮춰 다시 설치해준다.
   - `npm install react@#17 react-dom@17`
3. 그리고 `npm install`을 통해 패키지 설치를 완료 후 프로젝트를 실행하면 에러가 발생할 것이다.
   - 브라우저에 `react-dom/client`에러가 표시될텐데, 이는 `index.js` 파일에 `import ReactDOM from 'react-dom/client` 으로 확인할 수 있다.
   - 리액트 18에서 사용하는 `react-dom/client`에서 `react-dom` 으로 변경만 해주면 된다.
     - `import ReactDOM from 'react-dom'`
4. 그럼 또 에러가 발생하는데 `React DOM`에 관한 에러이다.
   - 리액트 18과 17에서 DOM을 구성하는 방식 차이로 인한 에러인데, `index.js` 파일에서 `createRoot`를 함수를 통해 root 객체를 만들어주고, 해당 객체를 `render`하는 방식으로 사용되고 있다.
   - 하위 버전인 리액트 17에서는 `ReactDOM`을 직접 `render`하는 방식으로 사용된다.

```jsx
import React from "react";
import ReactDOM from "react-dom";
import "./style/index.css";
import App from "./App";

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById("root")
);
```

### npm install @toast-ui/react-editor

- 정상적으로 설치가 될 것이다.
  **다운그레이드 완료**
