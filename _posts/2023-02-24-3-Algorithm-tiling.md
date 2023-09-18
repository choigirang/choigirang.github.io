---
layout: post
title: Algorithm 6장 - tiling
author: admin
date: 2023-02-24 00:00:00 +900
lastmod: 2023-02-24 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [ALGORITHM]
tags: [algorithm, tiling]
---

# tiling

## 문제

- 세로 길이 2, 가로 길이 n인 2 x n 보드가 있습니다.
- 2 x 1 크기의 타일을 가지고 이 보드를 채우는 모든 경우의 수를 리턴해야 합니다.

```js
let tiling = function (n) {};
```

## 입력

- n
  - `number` 타입의 1이상의 자연수

## 출력

- `number` 타입을 리턴해야 합니다.

## 주의사항

- 타일을 가로, 세로 어느 방향으로 놓아도 상관없습니다. (입출력 예시 참고)

## 입출력 예시

```js
let output = tiling(2);
console.log(output); // --> 2

output = tiling(4);
console.log(output); // --> 5
/* 
2 x 4 보드에 타일을 놓는 방법은 5가지
각 타일을 a, b, c, d로 구분

2 | a b c d
1 | a b c d 
------------

2 | a a c c
1 | b b d d 
------------

2 | a b c c
1 | a b d d 
------------

2 | a a c d
1 | b b c d 
------------

2 | a b b d
1 | a c c d 
------------
*/
```

## Advanced

- 타일링 문제를 해결하는 효율적인 알고리즘(O(N))이 존재합니다.
- 반드시 직접 문제를 해결하시면서 입력의 크기에 따라 어떻게 달라지는지 혹은 어떻게 반복되는지 관찰하시기 바랍니다.

## 풀이

1. 경우의 수를 계산하면서 규칙성을 찾아나간다.
   1. n이 1일 때, 오직 하나의 경우의 수만 가능하다.(l)
   2. n이 2일 때, 두 가지 경우의 수가 가능하다.(ll,=)
   3. n이 3일 때, 세 가지 경우의 수가 가능하다.(lll,l=,=l)
   4. n이 4일 때, 다섯 가지 경우의 수가 가능하다.(llll,ll=,l=l,==,=ll)
2. n이 3일 때부터는, n이 2인 경우의 모양이 포함되어 있다는 것을 알 수 있다.
3. n이 4인 경우에도, n이 2인 경우와 3인 경우가 포함되어 있다.
4. 따라서 재귀함수를 사용하고 피보나치 수열과 유사한 형태를 가진다.
5. 재귀를 한 함수 안에 두 번 사용하는 것으로 풀 수 있지만, 시간복잡도가 늘어나 N이 커지면 실행 시간이 매우 늘어난다.

```js
let tiling = function (n) {
  if (n <= 2) return n;
  return tiling(n - 1) + tiling(n - 2);
};
```

6. 동일한 계산을 반복해야 할 때, 이전에 게산한 값을 메모리에 저장하여 동일한 계산의 반복 수행을 제거하는 방법이다.
   1. `aux`라는 함수에 우리가 구하고자 하는 `n`을 전달인자로 넘긴다.
   2. `memo`의 `n`아 2보다 같거나 작을 경우, 우리가 작성해놓은 더미 데이터를 내보낸다.
   3. `memo`의 `n`이 정해지지 않았을 경우 `memo`의`size`는 `aux`에 -1과 -2를 한 값이 된다.
   4. 이와 같은 방법으로 한 번 구해진 값을 다시 구하지 않아 시간 복잡도를 단축할 수 있다.

```js
let tiling = function (n) {
  const memo = [0, 1, 2];
  const aux = (size) => {
    if (memo[size] !== undefined) return memo[size];
    if (size <= 2) return memo[n];
    memo[size] = aux(size - 1) + aux(size - 2);
    return memo[size];
  };
  return aux(n);
};
```

7. 데이터를 테이블에 정리하면서 bottom-up 방식으로 해결하는 기법이다.
   1. `memo`의 `n`번째 인덱스 값을 피보나치 수열과 같은 방법으로 `n`의 값을 구한다.
   2. 반복문을 한 번만 사용하기 때문에 시간이 비약적으로 줄어든다.
   ```js
   let tilitn = function(n){
    const memo = [0,1,2]
    if(n<=2) return memo[n]
    for(let size = 3; size <= n; size++){
      memo[size] memo[size-1] + memo[size-2]
    }
    return memo[n]
   }
   ```
