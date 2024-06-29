---
layout: post
title: Javascript 23장 - Object.is, 반복문
author: admin
date: 2024-06-30 00:00:00 +900
lastmod: 2024-06-30 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [JAVASCRIPT]
tags: [object, is, for, dowhile, while, continue, break]
---

# Javascript

- 처음부터 다시 보는 딥 다이브

## Object.is

- ES6에서 도입된 메서드며 일치 비교 연산자와 동일하게 작동하지만 더 엄격하게 평가된다.
- -0, +0은 같은 값과 타입으로 일치하지만, 이 메서드는 IEEE 표준에 따라 다른 값으로 취급하며 비트 표현까지 고려하여 비교한다.
- 유효하지 않은 숫자 상태를 나타내는 **개념**을 가진 NaN과 NaN 또한 동일하다고 판단한다.

```jsx
Object.is(-0, +0); // true
Object.is(NaN, NaN); // true
```

## 반복문

### for

- 반복 횟수가 명확할 때 주로 사용한다.
- for 반복문은 괄호 안에 3가지로 구성된다.

  1. 변수 선언
  2. 조건식 평가
  3. 증감식

- 변수 선언은 최초에 한 번만 이루어지고, **조건식 평가가 참일 때 코드 블록을 실행한다.**
- 코드 블록이 먼저 실행되고 증감식이 실행된다.
- 코드 블록이 실행되고 이후 증감식이 실행되는 흐름이 중요하다.

### while

- 평가 결과가 참이면 코드 블록을 계속해서 실행한다.
- 반복 횟수가 불명확할 때 주로 사용한다.
- 평가 결과가 거짓이 되면 코드블록을 실행하지 않고 종료하고, 평가 결과가 참이면 코드 블록이 실행된다.
- 항상 참일 경우 **무한 루프가 되며, if 문으로 탈출 조건을 만들고 break문으로 코드 블록을 탈출한다.**

```jsx
let i = 10;

while (i <= 0) {
  i--;
}

let j = 0;

while (true) {
  j++;
  if (j === 10) break;
}
```

### do...while

- do 코드 블록을 먼저 실행하고 조건식을 평가한다.
- 따라서 코드 블록이 한 번 이상은 무조건 실행된다.

```jsx
let i = 0;

do {
  i++;
} while (i <= 10);
```

### continue

- break가 탈출을 의미하면, continue는 실행 시점을 중단하고 증감식을 실행한다.

```jsx
for (let i = 0; i <= 10; i++) {
  if (i === 9) continue;
  console.log(i);
}
// console에 9는 찍히지 않고 => console.log(i)가 실행되지 않고
// 증감식 실행, i === 9일 때 바로 + 1
```
