---
layout: post
title: TypeScript 1장 - 기본 개념
author: admin
date: 2023-02-28 00:00:00 +900
lastmod: 2023-02-28 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [TYPESCRIPT]
tags: [typescript, javascript, compiler, runtime]
---

# TypeScript

## 개념

- 2012년 마이크로소프트에서 발표한, 자바스크립트 기반의 정적 타입 문법을 추가한 프로그래밍 언어이다.
  - 정적타입이란, 데이터의 타입(`number`,`string`,`boolean` 등)을 미리 정해놓고 사용하는 것이다.
  - 자바스크립트는 동적 타입을 갖는 것과 반대된다.
  - 타입스크립트는 코드 작성 단계에서 타입 오류를 확인할 수 있고, 자바스크립트는 런타임에서 동작할 때 타입 오류를 확인할 수 있다.
  - 타입스크립트를 Node.js나 브라우저에서 바로 확인할 수 없기 때문에 자바스크립트로 변환 후 확인해야 한다.(자바스크립트로 `컴파일` 한다.)
  - 정적 타입의 컴파일 언어
- 자바스크립트와 **슈퍼셋**으로 완벽하게 호환한다.
  - 자바스크립트의 기능을 거의 다 가지고 있고 추가적인 기능을 갖고 있다.
  - 대부분의 라이브러리, 프레임워크가 타입스크립트를 지원한다.
  - 자바스크립트보다 타입스크립트가 더 상위 개념이다.
  - `constructor`에서 사용하는 `super()`와 같은 개념이다.
  - 자바스크립트의 문법을 기반으로 추가적으로 배우는 것이다.

## 개요 및 개발 환경 구성

- `npm init -y`를 통해 패키지를 설치한다.
- `npm i -D typescript`를 설치한다.
  - 개발 의존성 패키지
  - `devDependencies`를 확인해보면 `typescript`패키지와 `parcel`번들러가 설치된 것을 확인할 수 있다.
  - `sciprts`를 `"dev" : "parcel ./index.html"`로 바꿔 현재 프로젝트의 `index.html`을 진입점으로 바꿔준다.
  - `"build" : "parcel build ./index.html"`을 추가로 작성한다.
- `index.html`파일을 만들고 우리가 만들 타입스크립트 파일을 연결해주어야 한다.
  - 자바스크립트의 `js`, 타입스크립트는 `ts`를 사용한다.
  - 타입스크립트를 만들고 `index.html`에서 파일을 연결한다.
  ```jsx
  <script type="module" defer src="./src/main.ts"></sciprt>
  ```
  - `parcle` 번들러가 읽을 수 있도록 작성한 것이다.
- `tsconfig.json`을 만든다.
