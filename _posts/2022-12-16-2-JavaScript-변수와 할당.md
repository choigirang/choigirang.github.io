---
layout: post
title: JavaScript 3장 - 변수와 할당
author: admin
date: 2022-12-16 00:00:00 +900
lastmod: 2022-12-16 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [JAVASCRIPT]  # 대문자로 작성
tags: [javascript, 변수, 할당, 백틱, 카멜케이스, 템플릿리터럴, 식별자, 예약어, 재할당]
---
# 변수와 할당
- 구구단 예시
```js
console.log( 2 * 1 ); // 2
console.log( 2 * 2 ); // 4
console.log( 2 * 3 ); // 6
console.log( 2 * 4 ); // 8
console.log( 2 * 5 ); // 10
console.log( 2 * 6 ); // 12
console.log( 2 * 7 ); // 14
console.log( 2 * 8 ); // 16
console.log( 2 * 9 ); // 19
```
- 2단에 대한 구구단을 바꾸기 위해 숫자 2를 모두 3으로 바꿔 줘야 하는 번거로움이 있다.
- 이를 변수에 할당하여 간단하게 해결한다.
```js
let num = 3
console.log( num * 1 ); // 3
console.log( num * 2 ); // 6
console.log( num * 3 ); // 9
console.log( num * 4 ); // 12
console.log( num * 5 ); // 15
console.log( num * 6 ); // 18
console.log( num * 7 ); // 21
console.log( num * 8 ); // 24
console.log( num * 9 ); // 27
```
- `=` 연산자는 등호 기호가 아닌 할당 연산자를 의미한다.
    - 메모리 공간에 특정한 값을 넣는 것을 할당이라고 한다.
    - 변수명이 가진 메모리 공간을 확보하고, 이 메모리에는 값이 저장되어 있음을 의미한다.
```javascript
let num; // 변수 선언
num = 6; // 값의 할당
// let num = 6; 는 변수 선언과 값의 할당이 동시에 이루어진 것이다.
-
console.log( num * 1 ); // 6
console.log( num * 2 ); // 12
console.log( num * 3 ); // 18
console.log( num * 4 ); // 24
console.log( num * 5 ); // 30
console.log( num * 6 ); // 36
console.log( num * 7 ); // 42
console.log( num * 8 ); // 48
console.log( num * 9 ); // 54
```
- 값의 재할당
  - `var` 키워드드로도 변수를 선언할 수 있지만, 전역 변수로 선언되어 사이드 이펙트를 일으키기 때문에 잘 사용하지 않는다.
  - `let` 키워드로 선언한 변수에 새로운 값을 할당한다.
  - 이를 재할당이라고 하며, 재할당 시에도 `=` 연산자를 사용한다.
  - `const` 는 재할당이 불가능하다.
```js
let numA = 6; // 변수 선언과 값의 할당
console.log(numA * 1); // 6
numA = 7; // 값의 재할당
console.log(numA * 1); // 7
-
const numB = 0;
numB = 1 // error
```
- 변수명 규칙
    - 식별자는 특수문자를 제외한 문자, 숫자, 언더스코어 `_`, 달러 기호 `$` 를 포함할 수 있다.
    - 단, 식별자는 숫자를 제외한 문자, 언더스코어 `_`, 달러 기호 `$` 로 시작해야 한다.
    - 예약어 (함수에서 사용되는 메서드) 식별자로 사용할 수 없다.
    - 하나 이상의 단어를 사용할 때는 네이밍 컨벤션을 지켜 가독성을 높인다.
    - 일반적으로 카멜케이스(`camelCase`)를 사용한다.
```js
let name, $head, _score // 사용 가능 변수명
-
let 1st; // 변수 사용 불가
-
let const; // 변수 사용 불가
-
//변수의 존재 목적을 이해할 수 없는 변수명
let x = 100;
let y = 50;
-
//변수의 존재 목적을 명확히 알 수 있는 변수명
let name = 'kim';
let age = '25';
-
// 카멜 케이스
let firstName = 'girang';
let lastName = 'choi';
-
// 그 밖에 네이밍 컨벤션
let first_name; // 스네이크 케이스(snake_case) : 단어와 단어 사이에 언더스코어 사용
let FistName; // 파스칼 케이스(PascalCase) : 단어의 시작을 대문자로 작성
```
- 변수 활용
    - 변수에 숫자 타입의 값이 할당되어 있는 경우 모든 연산자를 사용할 수 있다.
    - 연산의 결과를 변수에 재할당 할 수 있다.
```js
let num = 3;
console.log(num + 1); // 4
console.log(num / 1); // 3
console.log(num % 2); // 1
-
// 변수의 재할당을 활용한 연산 값 대입
let num = 3;
num = num + 2;
console.log(num); // 5
```
- 템플릿 리터럴
    - 백틱을 사용하여 문자열 내부에 변수를 삽입할 수 있다.
    - 템플릿 리터널 내부에 `${ }`를 사용하여 변수를 삽이할 수 있으며, 문자열이 할당되지 않은 변수도 문자열로 취급한다.
    - 단어와 단어 사이에 공백을 삽입해야 하는 경우, 백틱을 사용하는 것보다 템플릿 리터럴을 사용하는 것이 가독성 측면에서 우수하다.
```js
//템플릿 리터럴
let name = 'kim';
let age = '25';
let location = 'seoul';
console.log('${name} ${age} ${seoul}'); // 'kim 25 seoul' --> 25는 문자열로 취급된다.
-
//템플릿 리터럴 사용 안할 시
let name = 'lim';
let age = '22';
let location = 'seoul';
console.log(name + ' ' + age + ' ' + location); // 'lim 22 seoul'
```