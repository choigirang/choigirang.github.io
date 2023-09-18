---
layout: post
title: JavaScript 1장 - 기본 개념
author: admin
date: 2022-12-15 00:00:00 +900
lastmod: 2022-12-15 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [JAVASCRIPT]  # 대문자로 작성
tags: [javascript, console, var, const, let] # 소문자로 작성
---
# 자바스크립트

- 자바스크립트는 콘텐츠를 동적으로 바꾸고 멀티미디어를 제어하는 등의 다양한 기능을 하는 프로그래밍 언어이다.
- 자바스크립트는 html/css와 함께 사용하지만 Node.js를 사용하여 독립적으로 사용이 가능하다.
- Java와는 엄연히 다른 프로그래밍 언어로, 브라우저뿐만 아니라 Node.js, REPL 등 다양한 환경에서 사용이 가능하다.

## 자바스크립트 실행 방식
- 자바스크립트는 코드를 위에서 아래로 읽고 해석하며 에러를 발견하는 해당 지점에서 코드를 즉시 중단한다.
- 브라우저, 서버, 모바일, 개발 등 전범위적인 개발이 가능한 프로그래밍 언어이다.
- 주석을 추가하여 코드 위 메모가 가능하다.

### 코드 정리
- `console.log( )`
    - 개발자 도구 콘솔이나 터미널에 원하는 값을 출력할 수 있게 도와주는 메서드이다.
```js
// 슬래시를 이용한 주석처리
console.log('hello World') // 'hello World'
console.log(2) // 2
```
- `let`
    - 변수 선언, 하나의 값을 저장한 이름이다.
- `const`
    - `let` 과 달리 재할당 (변수에 저장된 값을 다른 값으로 변경)이 불가능하다
```js
let school = 'student'
school = 'adult'
console.log(school) // 'adult'
-
const student = 'teacher'
student = 'adult' // error
```
- `var`
    - `let`과 `const` 같이 변수를 선언하는 용도로 사용되지만 전역 변수로 선언되기 때문에 잘 사용되지 않는다.