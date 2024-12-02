---
layout: post
title: Javascript 35장 - 배열 고차 함수
author: admin
date: 2024-07-12 00:00:00 +900
lastmod: 2024-07-12 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [JAVASCRIPT]
tags: [every, some, flatmap, map, sort, foreach, reduce, find, findindex]
---

# Javascript

- 처음부터 다시 보는 딥 다이브

## 배열 고차 함수

- 고차함수 : 함수를 인수로 전달받거나 함수를 반환하는 함수를 의미한다.
- 자바스크립트에서 함수는 객체이며, 일급 객체이기 때문에 함수를 값처럼 인수로 전달할 수 있으며 반환할 수도 있다.
- 고차함수는 `외부 상태의 변경이나 가변 데이터를 피하고 불변성을 지향하는 함수형 프로그래밍에 기반을 둔다.`
- `순수 함수와 보조 함수의 조합을 통해 조직 내에 존재하는 조건문과 반복문을 제거하여 복잡성을 해결하고 변수의 사용을 억제하여 상태 변경을 피하려는 프로그래밍 패러다임을 의미한다.`
- 자바스크립트 배열은 매우 유용한 고차 함수를 제공한다.

### Array.prototype.sort

- `mutator mothod`
- 배열의 요소를 정렬하고 정렬된 배열을 반환한다.
- 기본값으로는 오름차순으로 정렬된다.
- 숫자 타입이어도 암묵적으로 문자열 타입으로 변환 후 유니코드 순서에 따라 정렬한다.
  - 숫자 요소 정렬 시에는 정렬 순자를 정의하고 비교함수를 인수로 전달해주어야 한다.
- 비교 함수는 양수, 음수, 0을 반환해야 한다.

```jsx
const numArr = [3, 5, 7, 9, 1, 2, 4, 6, 8];
numArr.sort(); // [1,2,3,4,5,6,7,8,9]

const arr100 = [98, 99, 100, 101, 102];
arr100.sort((num) => num >= 100); // [100, 101, 102]

const arrNumUni = [40, 100, 1, 5, 2, 25, 10];
arrNumUni.sort(); // [1, 10, 100, 2, 25, 40, 5] 앞의 숫자에 따라 순서대로 (1 => 2 => 4 => 5)
arrNumUni.sort((a, b) => a - b); // [1, 2, 4, 5, 10, 25, 100]
arrNumUni.sort((a, b) => b - a); // [100, 25, 10, 5, 4, 2, 1]
```

### Array.prototype.forEach

- `accessor method`
- `for 문을 대체할 수 있는 고차함수이다.`
- 자신의 내부에서 반복문을 실행하며, 각 배열의 요소에 대해 한 번씩 콜백 함수가 호출된다.
- `콜백 함수는 일반 함수로 호출되기 때문에, this의 참조는 undefined가 바인딩된다. (고차함수에서는 strict mode가 반영되며, 아닐 경우 전역 객체를 가리킨다.)`
- `콜백 함수에서 호출한 this와 메서드 내부의 this를 일치시키려면, 화살표 함수나 this로 사용할 객체를 바인딩해야 한다.`
- 반환값은 undefined 이다.
- forEach 문 내부에서는 break, continue 문을 사용할 수 없기 때문에 순회 중간에 중단할 수 없다.
- **`for 문에 비해 기본 성능은 좋지 않으나 가독성은 좋다.`**
  - **`따라서, 대용량 데이터를 순회하거나 시간이 많이 걸리는 작업은 for문보다 forEach문이 선호된다.`**

```jsx
class Numbers {
  numArr = [];

  sum(arr) {
    arr.forEach(function (each) {
      this.numArr.push(each + each);
      // TypeError / this undefined
    });
  }
}

const numbers = new Numbers();
numbers.sum([1, 2, 3]);
```

```jsx
// 화살표 함수를 사용한 this 바인딩
class Numbers {
  numArr = [];

  sum(arr) {
    arr.forEach((each) => this.numArr.push(each + each));
  }
}

const numbers = new Numbers();
numbers.sum([1, 2, 3]);
numbers.numArr; // [2, 4, 6]
```

### Array.prototype.map

- `accessor method`
- 콜백 함수의 반환값들로 구성된 새로운 배열을 반환한다.
- 원본 배열의 요소값을 다른 값으로 매핑한 새로운 배열을 생성하기 위한 고차함수이다.

```jsx
const arr = [1, 2, 3];
const mappingArr = arr.map((each) => Math.pow(each, 2)); // [1, 4, 9]
```

### Array.prototype.filter

- `accessor method`
- `콜백 함수의 반환값이 true인 요소로만 구성된 새로운 배열을 반환한다.`
- 특정 조건에 맞는 배열의 요소들을 추출할 때 유용하게 사용된다.

```jsx
const arr = [98, 99, 100, 101, 102];
const over100 = arr.filter((each) => each >= 100); // [100, 101, 102]
```

### Array.prototype.reduce

- `accessor method`
- 콜백 함수를 호출하여 하나의 결과값을 만들어 반환한다.
- `매 차례마다, 콜백 함수의 반환값과 두 번째 요소값을 콜백 함수의 인수로 전달하면서 호출한다.`
- 4개의 매개 변수를 가진다.
  - accumulator : 누적된 값
  - currentValue : 현재 조회하는 값
  - index : 현재 조회하는 값의 인덱스
  - array : 원본 배열 참조 (this)

```jsx
const arr = [1, 2, 3, 4, 5];

const newArr = arr.reduce((acc, cur, idx, arr) => {
  return acc + cur;
}, 0);
// 0은 시작값
// 첫 번째 acc : 0, cur : 1, idx : 0, arr : [1,2,3,4,5]
// 두 번째 acc : 1, cur : 2, idx : 1, arr : [1,2,3,4,5]
// 세 번째 acc : 3, cur : 3, idx : 2, arr : [1,2,3,4,5]
// 네 번째 acc : 6, cur : 4, idx : 3, arr : [1,2,3,4,5]
// 다섯 번째 acc : 10, cur : 5, idx : 4, arr : [1,2,3,4,5]
// newArr = 15
```

```jsx
// 평균 구하기
const arr = [1, 2, 3, 4, 5];

const average = arr.reduce((acc, cur, idx, { length }) => {
  return idx === length - 1 ? (acc + cur) / length : acc + cur;
}, 0);
```

```jsx
// 최대값 구하기
const arr = [1, 2, 3, 4, 5];

const maxNum = arr.reduce((acc, cur) => (acc > cur ? acc : cur)); // 5
```

```jsx
// 요소의 중복 횟수 구하기
const arr = ["banana", "apple", "apple", "orange", "apple"];

const numFruit = arr.reduce((acc, cur) => {
  acc[cur] = (acc[cur] || 0) + 1;
  return acc;
}, {}); // { banana: 1, apple: 3, orange: 1 }
```

```jsx
// 중복 요소 제거
const arr = [1, 2, 1, 3, 5, 4, 5, 3, 4, 4];

const onlyOne = arr.reduce((acc, cur) => {
  if (!arr.indexOf(cur)) acc.push(cur);
  return acc;
}, []); //
```

### Array.prototype.some

- 콜백 함수의 반환값이 단 한 번이라도 참이면 true, 모두 거짓이면 false를 반환한다.
- `배열의 요소 중에서 콜백 함수를 통해 정의한 조건을 만족하는 요소가 1개이상 존재하는지 확인하여 그 결과를 boolean 타입으로 반환한다.`

```jsx
const arr = [1, 2, 3, 4, "5"];

const checkStr = arr.some((each) => typeof each === "string")
  ? "문자열이 있습니다."
  : "문자열이 없습니다.";
// 문자열이 있습니다.
```

### Array.prototype.every

- 콜백 함수의 반환값이 모두 참이면 true, 단 한 번이라도 거짓이면 false를 반환한다.
- `배열의 모든 요소가 콜백 함수를 통해 정의한 조건을 모두 만족하는지 확인하여 그 결과를 boolean 타입으로 반환한다.`
- `빈 배열은 항상 true를 반환한다.`

```jsx
const arr = [1, 2, 3, 4, "5"];

const checkStr = arr.every((each) => typeof each === "number")
  ? "모두 참입니다."
  : "하나는 거짓입니다.";
// 하나는 거짓입니다.
```

### Array.prototype.find

- ES6에 도입
- `자신을 호출한 배열의 요소를 순회하면서 인수로 전달된 콜백함수가를 호출하여 true를 반환하는 첫 번째 요소를 반환한다.`

```jsx
const arr = [
  { FirstName: "Girang", LastName: "Choi" },
  { FirstName: "Jiyoon", LastName: "Jeong" },
  { FirstName: "Nara", LastName: "Han" },
  { FirstName: "Jimin", LastName: "Choi" },
];

const findChoi = arr.find((each) => each.LastName === "Choi");
// {FirstName: "Girang", LastName: "Choi"}
```

### Array.prototype.findIndex

- ES6 도입
- `findIndex 메서드는 콜백 함수에 정의한 조건에 대해 true를 반환하는 요소의 인덱스를 반환한다.`
- true를 반환하는 요소가 존재하지 않을 경우 -1 을 반환한다.

```jsx
const arr = [
  { FirstName: "Girang", LastName: "Choi" },
  { FirstName: "Jiyoon", LastName: "Jeong" },
  { FirstName: "Nara", LastName: "Han" },
  { FirstName: "Jimin", LastName: "Choi" },
];

const findChoi = arr.find((each) => each.LastName === "Choi");
// 0
```

### Array.prototype.flatMap

- ES10에 도입
- `[Array.prototype.map] 메서드를 통해 생성된 배열을 평탄화한다.`
- map 함수 호출 결과가 flat 메서드로 호출한 결과와 같다.
- 단 flat 과정의 깊이를 1로만 적용할 수 있다.

```jsx
const arr = [1, 2, 3];

const mapArr = arr.map((each) => [each, each * 2]);
// [[1,2], [2,4], [3,6]]
const flatArr = arr.flatMap((each) => [each, each * 2]);
// [1,2, 2,4, 3,6]
```
