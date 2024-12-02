---
layout: post
title: Javascript 36장 - Number와 Math
author: admin
date: 2024-07-12 00:00:00 +900
lastmod: 2024-07-12 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [JAVASCRIPT]
tags: [abs, round, floor, ceil, random, sqrt, pow]
---

# Javascript

- 처음부터 다시 보는 딥 다이브

## Number 생성자 함수

- 표준 빌트인 객체인 Number 객체는 생성자 함수 객체이다.
- new 연산자와 함께 호출하여 Number 인스턴스를 생성할 수 있다.
- 인수를 전달하지 않고 생성하면 [[NumberData]]에 0을 할당한 Number 래퍼 객체를 생성한다.
- ES5에서는 [[PrimitiveValue]] 내부 슬롯이라 불렸다.
- 인수로 문자열을 숫자 값을 전달한 경우, 타입을 강제 변환 후 객체를 생성한다.
- 인수로 숫자가 아닌 문자열을 전달한 경우, NaN이 할당된 Number 객체를 생성한다.
- `new Number를 통해 추가적인 프로퍼티를 생성하여 사용할 수 있지만 예측 불가능한 동작을 일으킬 수 있기 때문에 권장되지 않는다.`
- 일반적으로 할당되는 원시값과 달리 객체를 생성하기 때문에 성능 면에서도 비효율적이다.

```jsx
new Number(2); // Bug Finder: Do not use Number as a constructor. 현재 X
```

## Math

- 표준 빌트인 객체이지만 생성자 함수는 아니다.
- 정적 메서드와 정적 프로퍼티만 제공된다.

### Math.abs

- 인수로 전달된 숫자의 절대값을 반환한다.
- 절대값은 반드시 0또는 양수여야 한다.

```jsx
Math.abs(-1); // 1
Math.abs("-1"); // 1
Math.abs(""); // 0
Math.abs([]); // 0
Math.abs(null); // 0
Math.abs(undefined); // NaN
Math.abs({}); // NaN
Math.abs("string"); // NaN
Math.abs(); // NaN
```

### Math.round

- 인수로 전달된 숫자의 소숫점 이하를 반올리한 정수를 반환한다.

```jsx
Math.round(1.4); // 1
Math.round(1.5); // 2
Math.round(-1.4); // -1
Math.round(-1.5); // -1
Math.round(-1.6); // -2
Math.round(); // NaN
```

### Math.ceil

- 인수로 전달된 숫자의 소숫점 이하를 올림한 정수를 반환한다.

```jsx
Math.ceil(1.4); // 2
Math.ceil(1.5); // 2
Math.ceil(-1.4); // -1
Math.ceil(-1.5); // -1
Math.ceil(-1.6); // -1
```

### Math.floor

- 인수로 전달된 숫자의 소숫점 이하를 내림한 정수를 반환한다.

```jsx
Math.floor(1.4); // 1
Math.floor(1.5); // 1
Math.floor(-1.4); // -2
Math.floor(-1.5); // -2
Math.floor(-1.6); // -2
```

### Math.sqrt

- 인수로 전달된 숫자의 제곱근을 반환한다.

```jsx
Math.sqrt(1); // 1
Math.sqrt(4); // 2
Math.sqrt(9); // 3
```

### Math.random

- 임의의 난수를 반환한다.
- 난수의 범위는 0 <= Math.random <= 1의 실수다.

```jsx
const random = Math.floor(Math.random() * 10 + 1);
```

### Math.pow

- 인수로 전달된 숫자의 거듭제곱한 결과를 반환한다.
- 인수는 2개를 전달받으며, 첫 번째 인수로는 밑, 두 번째 인수로는 지수를 전달받는다.
- ES7에서 `**` 연산자를 사용하여 이를 대체한다.

### Math.max, min

- 전달받은 인수 중에서 가장 크거나 작은 수를 반환한다.
- 인수가 전달되지 않으면 무한대를 반환한다.
- `배열의 요소 중 최대값을 구할 시 스프레드 문법을 사용하거나 apply를 사용한다.`

```jsx
const arr = [1, 2, 3, 4, 5];

Math.max(...arr);
Math.max.apply(null, arr);
```
