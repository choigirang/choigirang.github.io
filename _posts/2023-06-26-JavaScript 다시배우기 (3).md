---
layout: post
title: JavaScript 9장 - 개념짚기 (3)
author: admin
date: 2023-06-26 00:00:00 +900
lastmod: 2022-06-26 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [JAVASCRIPT]
tags: [javascript, js, var, let, hoisting, scope, const]
---

# JavaScript

## 변수와 호이스팅

- 변수에는 `var, let, const` 세 가지가 있다.
- **호이스팅**은 선언된 변수가 최상위에 선언된 것처럼 행동하는 것을 말한다.

```js
var name;
console.log(name); //undefined
name = "Mike";

let name;
console.log(name); //ReferenceError
name = "Mike";
```

- `let, const` 또한 호이스팅은 되지만 사용하지 못하는 이유는 **Temporal Dead Zone**때문이다.
- TDZ는 할당을 하기 전에는 사용할 수 없는데, 이는 코드를 예측 가능하게 하고, 잠재적인 버그를 줄이기 위함이다.

```js
// Temporal Dead Zone
console.log(name);
// ...
let name = "Mike";

let age = 30;
function ShowAge() {
  // Temporal Dead Zone
  console.log(age);
  // ...
  let age = 20;
}

ShowAge();
```

## 변수의 생성과정

- 변수 생성에는 3단계를 거친다.

  - 1. 선언 단계
  - 2. 초기화 단계
  - 3. 할당 단계

- `var`에서는 선언 및 초기화 단계가 동시에 일어나기 때문에 할당 전에 호출하면 에러가 발생하지 않고 `undefined`가 츌력된다.
- `let`은 선언 단계와 초기화 단계가 분리되어 있어 호이스팅 되면서 선언 단계가 이루어지지만 초기화 단계는 실제 코드에 도달해서 이루어지기 때문에 `Reference Error`가 발생한다.
- `const`는 위 3단계가 동시에 일어나기 때문에 선언과 동시에 할당을 해주어야 한다.
- 따라서, `var, let`은 선언은 해두고 나중에 할당하는 것이 가능하지만 `const`는 선언과 동시에 할당을 해주어야 한다.

```js
var name;
name = "Mike";

let age;
age = 30;

const gender; // Error
gender = "Male";
```

- `var`는 함수 스코프이고, `let, const`는 블록 스코프이다.

```js
function add(){
    // block level scope
}

if(){
    // block level scope
}

for(){
    // block level scope
}
```

## 블록 스코프와 함수 스코프

- `scope` : 변수에 접근할 수 있는 범위를 말하며, 새로운 함수가 생설될 때마다 새로운 스코프가 발생한다.
- 선언된 블록 내부에서만 사용할 수 있는 것을 블록 스코프라고 한다.
- `let, const`는 블록 스코프이기 때문에 선언된 `if`문 안에서만 사용이 가능하다.

```js
const age = 30;

if (age <= 30) {
  var txt = "성인";
}

console.log(txt); // "성인"
```

- 함수 스코프는 새로운 스코프가 생성되고 함수의 블록 내부에서만 접근할 수 있다.

```js
function Age() {
  var age = 30;
}

console.log(age); // Reference Error
```

### 정리

- `var`와 `let, const`의 차이
  - `var`는 함수 스코프 내부에서만 지역 변수로 유지되기 때문에, 함수를 제외한 여러 `문`안에서 선언된 변수는 전역 변수로 취급된다.
- 스코프는 크게 전역 스코프와 지역 스코프로 나뉘며, 지역 스코프에는 함수 스코프와 블록 스코프가 있다.
