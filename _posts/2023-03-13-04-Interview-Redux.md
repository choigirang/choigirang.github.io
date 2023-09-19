---
layout: post
title: Interview 12장 - Redux
author: admin
date: 2023-03-13 00:00:00 +900
lastmod: 2023-03-13 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [INTERVIEW]
tags: [redux]
---

# Interview

## Redux

### 상태관리 라이브러리의 필요성에 대한 설명

- 상태 관리 없이 리액트를 사용할 경우, 상위 컴포넌트에서 하위 컴포넌트로 props를 전달해주는 과정이 반복되어 일어난다.
- 하나의 상태 값을 공유하는 컴포넌트들의 상태 값은 상위 컴포넌트에 작성되기 때문에 끊임없이 props를 통해 전달되어야 하며 props지옥이 펼쳐진다.
- 이렇게 되면 상태 값을 관리하기도 힘들어지며 가독성도 떨어지기 때문에 유지보수 또한 어렵게 된다.
- 따라서 하나의 저장소에 상태를 저장시켜놓고 필요할 때마다 이를 불러와 사용하기 위해 상태관리 라이브러리가 필요하다.

### Redux의 주요 개념

- Redux는 store, state, reducer, action, dispatch가 있다.
- store
  - state가 보관되어 있고, 여러가지 내장함수들이 포함되어 있다.
  - 현재 상탯값을 불러오는 getState(), action을 운반하는 dispatch, 상태를 수정하는 reducer 등이 담겨있다.
- state
  - 객체로, 어떠한 초기값이 들어가 있다.
  - 객체의 불변성이 지켜져야 한다.
- reducer
  - state를 수정할 수 있는 권한을 가진 유일한 함수이다.
  - 불변성을 고려하여 객체를 수정하는 것이 아닌 새로운 객체로 기존의 state를 대체한다.
- action
  - 어떠한 action이 일어났을 때, 어떠한 값을 전달할지 결정한다.
- dispatch
  - reducer에게 action을 넘겨주는 역할을 한다.
