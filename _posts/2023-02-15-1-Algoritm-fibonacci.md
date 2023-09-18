---
layout: post
title: Algorithm 2장 - fibonacci
author: admin
date: 2023-02-15 00:00:00 +900
lastmod: 2023-02-15 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [ALGORITHM]  # 대문자로 작성
tags: [algorithm, fibonacci]
---
# fibonacci
## 문제
- 아래와 같이 정의된 피보나치 수열 중 n번째 항의 수를 리턴해야 합니다.
  - 0번째 피보나치 수는 0이고, 1번째 피보나치 수는 1입니다. 그 다음 2번째 피보나치 수부터는 바로 직전의 두 피보나치 수의 합으로 정의합니다.
  - 0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, ...


```js
function fibonacci(n) {
    
}
```

## 입력
- n
  - `number` 타입의 n(n은 0이상의 정수)

## 출력
- `number` 타입을 리턴한다.

## 주의사항
- 재귀함수를 이용해 구현해야 합니다.
- 반복문(`for`, `while`) 사용은 금지됩니다.
- 함수 `fibonacci`가 반드시 재귀함수일 필요는 없습니다.


## 입출력 예시
```js
let output = fibonacci(0);
console.log(output); // --> 0

output = fibonacci(1);
console.log(output); // --> 1

output = fibonacci(5);
console.log(output); // --> 5

output = fibonacci(9);
console.log(output); // --> 34
```

## 풀이
1. 피보나치의 0번째 피보나치는 0이고, 1번째 피보나치는 1이다. 그 다음 2번째 피보나치 수부터 합으로 정의하기 때문에 값을 미리 저장한다.
2. 함수를 하나 더 만들고, 인자로 n을 넘겨준다.
   1. 만약 n번째 피보나치 수가 저장되지 않았다면, n번째 숫자를 구하기 위해 재귀함수를 돌려 n-1번 째와 n-2번 째 숫자를 더해 n번째 숫자를 정의한다.
3. 새롭게 만든 함수에 n을 전달한다.


```js
function fibonacci(n) {
  let result = [0,1]

  let fibonaccici= (n) =>{
    if(result[n] === undefined){
      result[n] = fibonaccici(n-1) + fibonaccici(n-2)
    }
    return result[n]
  }

  return fibonaccici(n)
}
```