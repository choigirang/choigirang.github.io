---
layout: post
title: Javascript 28장 - 함수의 프로퍼티와 일급 객체
author: admin
date: 2024-07-09 00:00:00 +900
lastmod: 2024-07-09 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [JAVASCRIPT]
tags: [function, proto, prototype, name]
---

# Javascript

- 처음부터 다시 보는 딥 다이브

## 일급 객체

- 다음의 조건을 충족하는 객체를 일급 객체라고 한다.
  - 런타임에 생성이 가능하다.
  - 변수나 자료구조(객체, 배열 등)에 저장할 수 있다.
  - 함수의 매개변수에 전달할 수 있다.
  - 함수의 반환값으로 사용할 수 있다.

```jsx
const increase = function (num) {
  return ++num;
};

const decrease = function (num) {
  return --num;
};

const predicate = { increase, decrease };

function makeCounter(predicate) {
  let num = 0;

  return function () {
    num = predicate(num);
    return num;
  };
}

const increaser = makeCounter(predicate.increase);
const decreaser = makeCounter(predicate.decrease);

console.log(increaser()); // 1
console.log(increaser()); // 2
console.log(decreaser()); // -1
console.log(decreaser()); // -2
```

### 일급 객체의 특징

- 일반 객체와 같이 함수를 함수의 매개변수로 전달할 수 있다.
- 함수의 반환값으로 함수를 사용할 수 있다.
- 일반 객체는 호출할 수 없지만 함수 객체는 호출이 가능하며, 함수 객체는 고유의 프로퍼티를 소유한다.

## 함수 객체의 프로퍼티

- 함수도 객체이기 때문에 프로퍼티를 가질 수 있다.

### arguments 프로퍼티

- `arguments 객체는 함수 호출 시 전달된 인수의 정보를 담고 있는 순회 가능한 유사 배열 객체이다.`
- 함수 내부에서 지역 변수처럼 사용할 수 있는 arguments 객체를 참조한다.
- 자바스크립트에서는 함수의 매개변수와 인수의 개수가 일치하는지 확인하지 않는다.
  - 따라서 함수 호출 시 매개변수 수만큼 인자를 전달하지 않아도 에러가 발생하지 않는다.
  - `함수를 정의할 때 선언한 매개변수는 호출되면 함수 몸체 내에서 암묵적으로 선언되고, undefined로 초기화된 후, 인수가 할당된다.`
- 매개변수보다 인수를 많이 전달했을 경우, 초과된 인수는 무시되지 않고 arguments 객체의 프로퍼티에 보관된다.
  - arguments 객체는 기본적으로 length 프로퍼티를 지원하며, 객체는 유사배열 객체이기 때문에 사용이 가능하다.
- `arguments는 유사 배열 객체이며 배열 그 자체는 아니다.`
  - 배열 그 자체가 아니기 때문에 배열 메서드를 사용할 수 없다.
  - 보완할 방법으로 ES6에 도입된 Rest 파라미터를 사용한다.

```jsx
function example() {
  console.log(arguments.length);
  console.log(arguments.map((i) => i)); // Error
}

// es6
function example(...args) {
  console.log(Array.isArray(args)); // true
}
```

### length 프로퍼티

- 함수 객체의 `length 프로퍼티는 함수를 정의할 때 선언한 매개변수의 갯수를 의미한다.`

```jsx
function example(x, y, c) {
  console.log(example.length); // 3
}
```

### name 프로퍼티

- 함수 객체의 `name 프로퍼티는 함수의 이름을 나타낸다.`
- 익명 함수의 경우 ES5에서는 빈 문자열, ES6는 함수 객체를 가리키는 식별자를 나타낸다.

```jsx
const funcName = function example() {};
console.log(funcName.name); // example

const funcName = function () {};
console.log(funcName.name); // funcName

function funcName() {}
console.log(funcName); // funcName
```

### proto 프로퍼티

- 모든 객체는 [[Prototype]] 라는 내부 슬롯을 갖는다.
- `[[Prototype]] 내부 슬롯은 Prototype 객체를 가리킨다.`
- `__proto__` 프로퍼티는 `[[Prototype]] 내부 슬롯이 가리키는 프로토타입 객체에 접근하기 위해 사용하는 접근자 프로퍼티이다.`
- 이 프로토타입에는 직접적으로 접근할 수 없고 간접적인 방법으로 프로토타입에 접근할 수 있다.

```jsx
const obj = { a: 1 };

console.log(obj.__proto__ === Object.prototype); // true
console.log(obj.hasOwnProperty("a")); // true
console.log(obj.hasOwnProperty("__proto__")); // false
// __proto__ 접근자 프로퍼티는 직접 접근할 수 있는 프로토타입이 아니다.
```

### prototype 프로퍼티

- `생성자 함수로 호출할 수 있는 함수 객체, constructor 만이 소유할 수 있는 프로퍼티이다.`
- `일반 객체나 non-constructor는 프로퍼티가 없다.`

```jsx
console.log(function () {}.hasOwnProperty("prototype")); // true
console.log({}.hasOwnProperty("prototype")); // false
```
