---
layout: post
title: JavaScript 2장 - 타입과 변수
author: admin
date: 2022-12-16 00:00:00 +900
lastmod: 2022-12-16 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [JAVASCRIPT]  # 대문자로 작성
tags: [javascript, console, type, number, boolean, string, math, index, let, const, var]
---
# 자바스크립트 타입
- `number`
- `string`
- `boolean`

## Number
- 자바스크립트에서 숫자를 표기하기 위한 데이터 타입이며 정수와 실수를 모두 표현할 수 있다.
```js
100;
-100;
100.1;
```
- `typeof` 연산자로 해당 값이 숫자 타입인지 알 수 있다.
```js
typeof 100; // 'number'
typeof -100; // 'number'
typeof 100.1; // 'number'
```
- 같은 숫자 값 간에는 사칙연산이 가능하다. (`+`,`-`,`*`,`/`,`%`) 이러한 숫자 기호를 산술 연산자라고 부른다.
```js
console.log(1 + 2); // 3
console.log(1 - 2); // -1
console.log(1 * 2); // 2
console.log(1 / 2); // 0.5
console.log(2 % 2); // 0
```
- `Math` 내장 객체
    - `Math.floor()` : 괄호 안의 숫자를 내림하여 반환한다.
    - `Math.ceil()` : 괄호 안의 숫자를 올림하여 반환한다.
    - `Math.round()` : 괄호 안의 숫자를 반올림하여 반환한다.
    - `Math.abs()` : 괄호 안의 숫자의 절댓값을 반환한다.
    - `Math.sqrt()` : 괄호 안의 숫자의 루트값을 반환한다.
    - `Math.pow()` : 괄호 안의 첫 번쨰 숫자를 밑, 두 번째 숫자를 지수인 숫자로 반환한다.
```javascript
Math.floor(100.1); // 100
Math.ceil(100.1); // 101
Math.round(100.6); // 101
Math.abs(-100); // 100
Math.sqrt(4); // 2
Math.sqrt(2); // 1.41421356237...
Math.pow(2, 5); // 32 2의 5제곱
```

## String
- 자연어를 JavaScript에서 표현하기 위한 데이터 타입으로, 따옴표('),쌍따옴표("),백틱(`)으로 감싸면 된다.
- 맥북의 경우 백틱은 `Alt`+`~` 을 누르면 입력할 수 있다.
- 한자나 이모지 또한 문자열로 만들 수 있고 숫자와 문자 조합도 가능하다.
- 백틱으로 만든 문자열은 줄바꿈도 가능하다. 
```js
'사과'
"JavaScript"
`Java`
```

- `+`로 문자열을 이어붙일 수 있다. 문자열과 문자열을 이어붙일 때는 문자열 연결 연산자로 쓰이며, 다른 타입과 이어붙이려고 하면 모두 문자열로 변환한다.
```js
"안녕하세요" + "!!" // 안녕하세요!!
'감사합니다' + ' ' + '~' // 감사합니다 ~
1 + '1' // 11문자
```
- 문자열의 `length` 속성을 이용하여 문자열의 길이를 확인할 수 있다.
- 문자열 값에 `.length` 를 붙이면 된다.
```js
let str = 'string'
console.log(str.length) // 6
```
- 문자열의 각 문자는 순서를 가지고 있는데 각 문자가 몇 번째 위치하는지 인덱스(index)로 확인할 수 있다. 첫 번째 문자의 인덱스는 `[]`이며, 이를 Zero-based numbering이라고 한다.
```js
let str = 'student'
console.log(str[1]) // 't'
console.log(str[5]) // 'n'
```
- `toLowerCase()` : 문자열을 소문자로 변경한다.
- `toUpperCase()` : 문자열을 대문자로 변경한다.
- `concat()` : 문자열 연결 연산자 `+` 처럼 문자열을 이어붙일 수 있다.
- `slice()` : 문자열의 일부를 자를 수 있다.
```js
let lowerStr = 'banana'
lowerStr.toUpperCase() // 'BANANA'
-
let upperStr = 'APPLE'
upperStr.toLowerCase() // 'APPLE'
-
let str = 'under'
let concatStr = 'BAY'
str.concat(concatStr) // 'underBAY'
-
let sliceStr = 'BANANA'
sliceStr.slice(0,-1) // 'BANAN'
sliceStr.slice(0) // 'BANANA'
sliceStr.slice(1) // 'ANANA'
sliceStr.slice(0,3) // 'BANA'
```

- `indexOf`
    - 문자열이나 배열의 인덱스를 나타낸다.
    - 특정 문자나 값이 문자열이나 배열 안에 있는지 찾을 때도 활용할 수 있다.
    - 일치하는 문자나 값이 없을 경우 -1을 리턴한다.
```js
let str = 'GaNaDaRaMaBaSa'
str.indexOf(3) // 'a'
str.indexOf('T') // -1
```
- `includes`
    - 문자열 내에 특정 문자나 문자가 포함되어 있는지 확인한다.
    - 문자가 있으면 `true`, 문자가 없으면 `false` 를 리턴한다.
```js
let str = 'ABCDE'
str.includes('A') // true
str.includes('a') // false
```

## boolean
- 사실 관계를 구분하기 위한 타입으로 `true` 나 `false` 를 리턴한다.
    - 비교연산자로 두 값이 같은지 다른지를 확인할 때 유용하다.
    - 값은 아니지만 `false`로 여겨지는 값을 `falsy`, 반대로 `true` 값은 `truthy` 라고 하는데, `truthy` 값은 매우 많아 `falsy` 값을 외우는 것이 좋다.
```js
false
0
-0
0n
""
''
``
null
undefined
NaN
```
- `===` , `!==`
    - 엄격한 동치 연사자로 두 피연산자 **값**과 **타입**이 같으면 `true`, 다르면 `false` 를 리턴한다.
    - 값이 같아도 타입이 다르면 `false` 를 리턴한다.
    - 원시형이 아닌 참조값은 주솟값도 확인하기 때문에 배열이나 객체의 값이 같아도 주솟값이 다르면 `false`를 리턴한다.
```js
123 === (100+23); // true
123 === '123'; // false
123 !== '123'; // true
123 !== (100+23); // false
-
let arrA = [1,2,3]
let arrB = [1,2,3]
arrA === arrB // false
```
- `==` , `!=`
    - 느슨한 동치 연산자로 **대체로** 값이 같으면 `true` 를 리턴한다.
    - 타입이 달라도 값이 같으면 **대체로** 같은 것이라 취급한다.
    - 느슨하게 여부를 판단하기 때문에 사용하지 않는 것을 권장한다.
```js
12 != '12' // true
```
- `>=`,`<=`,`>`,`<`
    - 비교연산자로 대소 관계를 판별하며 수학에서의 부등호 기호와 유사하다.
```js
100 > 200; // false
100 < 200; // true
100 >= 100; // true
200 <= 100; // false
```
- `&&`,`||`
    - 논리연산자로 `||` 는 `OR`의 의미를 가져 두 값 중 하나라도 맞다면 `true` , `&&` 는 `AND`의 의미를 가져 두 값 중 하나라도 다르면 `false` 를 리턴한다.
```js
ture || false; // true
false || true; // true
100 > 200 || 200 > 100; // true
-
true && true; // true
200 > 100 && 300 > 200; // true
100 > 200 && 100 > 50; // false
```
- `!`
    - 사실 관계를 반대로 표현한다.
```js
!true // false
!false // true
!0 // true
!NAN // true
!1 // false
```