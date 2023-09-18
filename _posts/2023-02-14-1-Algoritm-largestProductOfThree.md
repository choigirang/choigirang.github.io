---
layout: post
title: Algorithm 1장 - largestProductOfThree
author: admin
date: 2023-02-14 00:00:00 +900
lastmod: 2023-02-14 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [ALGORITHM]  # 대문자로 작성
tags: [algorithm]
---
# largestProductOfThree
## 문제
- 정수를 요소로 갖는 배열을 입력받아 3개의 요소를 곱해 나올 수 있는 최대값을 리턴해야 합니다.


```js
const largestProductOfThree = function (arr) {
    
}
```

## 입력
- arr
  - `number` 타입을 요소로 갖는 배열

## 출력
- `number` 타입을 리턴한다.

## 주의사항
- 입력으로 주어진 배열은 중첩되지 않은 1차원 배열입니다.
- 배열의 요소는 음수와 0을 포함하는 정수입니다.
- 배열의 길이는 3 이상입니다.


## 입출력 예시
```js
let output = largestProductOfThree([2, 1, 3, 7]);
console.log(output); // --> 42 (= 2 * 3 * 7)

output = largestProductOfThree([-1, 2, -5, 7]);
console.log(output); // --> 35 (= -1 * -5 * 7)
```

## 풀이
1. 배열을 오름차순으로 정렬한다.
   1. `sort`는 문자열의 크기를 비교하기 때문에 `number`타입을 정렬할 시 인자를 전달해야 한다.
2. `max`는 배열의 끝에서부터 3개의 숫자들을 곱한다.
3. `min`는 배열의 앞에서부터 3개의 숫자들을 곱한다.
   1. 배열의 요소들에 `음수 * 음수`가 있는 경우를 생각한다.
4. 계산된 숫자들 중에서 가장 큰 값을 리턴한다.


```js
const largestProductOfThree = function (arr) {
  arr.sort((a,b) => a-b)
  let max = arr[arr.length-1] * arr[arr.length-2] * arr[arr.length-3]
  let min = arr[0] * arr[1] * arr[arr.length-1]
  return Math.max(max,min)
};
```