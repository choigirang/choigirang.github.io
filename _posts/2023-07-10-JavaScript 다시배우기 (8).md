---
layout: post
title: JavaScript 14장 - 개념짚기 (8)
author: admin
date: 2023-07-10 00:00:00 +900
lastmod: 2022-07-10 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [JAVASCRIPT]
tags: [javascript, js]
---

# JavaScript

## 배열

### sort

- 배열을 재정렬하며, 배열 자체가 변경되니 주의해야 한다.
- 숫자를 가진 배열을 정렬한다고 해도 문자로 취급되어 정렬하기 때문에, `13,27,5,8` 이라는 숫자가 있을 때, 1과 2를 가진 13과 27이 앞에 오게 된다.
- 따라서, 제대로 된 정렬을 해주고 싶을 때는 인수로 비교할 수 있는 정렬 로직을 담은 함수를 전달해야 한다.
- `a`와`b`를 비교하여, `a-b`가 음수일 때는 `a`가 더 작다는 것이기에 `a`를 앞으로 보낸다.

```js
let arr = [1, 5, 4, 2, 3];
arr.sort(); // [1,2,3,4,5]

let str = ["a", "c", "b", "d", "e"];
str.sort(); // ["a","b","c","d","e"]

let random = [27, 8, 5, 13];
random.sort(); // [13,27,5,8]

function fn(a, b) {
  return a - b;
}

random.sort(fn); // [5, 8, 13, 27]
```

### \_

- 라이브러리로 **로대쉬**라고 불리는 언더스코어는 숫자든,문자든,객체든 원하는 기준으로 정렬되며 실무에서 많이 사용된다.

```js
_.sortBy(arr);
```

### reduce

- 누적된 값에 현재 값을 더한다.
- 인자로는 초깃값과 현재값을 넘겨주고, 옵션으로 초깃값이 얼마인지 작성한다.

```js
let num = [1, 2, 3, 4, 5];
num.reduce((pre, cur) => {
  return pre + cur;
}, 0); // 15

num.reduce((pre, cur) => {
  return pre + cur;
}, 100); // 115
```

- `map`을 사용하지 않고 새로운 배열을 재구성할 수 있다.

```js
let users = [
  { name: "Mike", age: 20 },
  { name: "Jane", age: 10 },
  { name: "John", age: 30 },
  { name: "Kane", age: 50 },
  { name: "Bill", age: 60 },
  { name: "Choi", age: 18 },
];

users.reduce((pre, cur) => {
  if (cur.age > 19) pre.push(cur.name);
  return prev;
}, []);
// ["Mike","John","Kane","Bill"]
```

## 구조분해할당

- 배열이나 객체의 속성을 분해해서 그 값을 변수에 담은 표현식을 말한다.

```JS
let [x,y] = [1,2];
x; // 1
y; // 2
```

- 해당하는 값이 없을 경우 `undefined`가 설정되지만, 기본값을 설정하여 에러를 방지할 수 있다.

```js
let [a, b, c] = [1, 2];
a; // 1
b; // 2
c; // undefined

let [a = 0, b = 1, c = 3] = [1, 2];
a; // 1
b; // 2
c; // 3
```

### 배열구조분해

- 건너뛰고 싶은 값이 있다면 빈 값을 넣어주면 된다.

```js
let [a, , c] = ["Mike", "Jane", "John"];
a; // Mike
c; // John
```

- 만약 일반적인 변수의 값을 바꾸기 위해서는 의미없는 변수를 하나 더 생성한 후, 값을 옮기고 이를 할당해야만 했지만 구조분해할당을 사용하면 쉽게 바꿀 수 있다.

```js
let a = 1;
let b = 2;
// a와 b 바꾸기
let c = a;
// a=b를 선언해버리면 a 값이 없어진다.
a = b; // 2
b = c; // 1

// 구조분해할당
[a, b] = [b, a];
```

### 객체구조분해

- 객체 또한 구조분해할 수 있으며, 순서를 신경쓰지 않아도 된다.
- 변수명 또한 바꿀 수 있다.

```js
let user = { name: "Mike", age: 30 };

let { name, age } = user;
// 위 코드는 아래의 코드와 동일하다.
let name = user.name;
let age = user.age;

let { name: userName, age: userAge } = user;
userName; // Mike
userAge; // 30
```

- 기본값 또한 설정할 수 있는데, 받은 값이 `undefined`일 때만 기본값이 사용된다.

```js
let user = {
  name: "Mike",
  age: 30,
  gender: "female",
};

let { name, age, gender = "male" } = user;

gender; // female
```
