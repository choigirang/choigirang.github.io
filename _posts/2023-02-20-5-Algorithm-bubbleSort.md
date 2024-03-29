---
layout: post
title: Algorithm 3장 - bubbleSort
author: admin
date: 2023-02-20 00:00:00 +900
lastmod: 2023-02-20 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [ALGORITHM]  # 대문자로 작성
tags: [algorithm, fibonacci, bubbleSort]
---
# bubbleSort
## 문제
- 정수를 요소로 갖는 배열을 입력받아 오름차순으로 정렬하여 리턴해야 합니다.
- 버블 정렬(bubble sort)은 여러 정렬 알고리즘(삽입 정렬, 퀵 정렬, 병합 정렬, 기수 정렬 등) 중 가장 기본적인 알고리즘입니다.
  1. 첫 번째 요소가 두 번째 요소보다 크면, 두 요소의 위치를 바꿉니다. (swap)
  2. 두 번째 요소와 세 번째 요소보다 크면, 두 요소의 위치를 바꿉니다. (swap)
  3. 1, 2를 마지막까지 반복합니다. (마지막에서 두 번째 요소와 마지막 요소를 비교)
  4. 1~3의 과정을 한 번 거치게 되면, 가장 큰 요소가 배열의 마지막으로 밀려납니다.
  5. 1~3의 과정을 첫 요소부터 다시 반복합니다.
  6. 5를 통해 두 번째로 큰 요소가 배열의 마지막 바로 두 번째로 밀려납니다.
  7. 1~3의 과정을 총 n번(배열의 크기) 반복합니다.


```js
let bubbleSort = function (arr) {
    
}
```

## 입력
- arr 
  - `number` 타입을 요소로 갖는 배열
  - `arr[i]`는 정수
  - `arr[i]`의 길이는 1,000 이하

## 출력
- `number` 타입을 요소로 갖는 배열을 리턴해야 합니다.
- 배열의 요소는 오름차순으로 정렬되어야 합니다.
- `arr[i] <= arr[j]` (`i < j`)

## 주의사항
- 위에서 설명한 알고리즘을 구현해야 합니다.
- `arr.sort` 사용은 금지됩니다.
- 입력으로 주어진 배열은 중첩되지 않은 1차원 배열입니다.


## 입출력 예시
```js
let output = bubbleSort([2, 1, 3]);
console.log(output); // --> [1, 2, 3]
```

## Advanced
- 아래의 힌트를 바탕으로 (최선의 경우) 수행 시간을 단축할 수 있도록 코드를 수정해보세요.
- 위에서 설명된 알고리즘 1~3의 과정 중 어떤 요소도 위치가 바뀌지 않은 경우, 배열이 정렬된 상태라는 것을 알 수 있습니다.

## 풀이
1. 두 변수를 바꾸는 법 중 하나는 임시 변수를 선언하여 그 값에 바꾸고 싶은 변수 중 하나인 `a`를 할당하고, 나머지 하나의 변수값으로 값을 재구성한다.
   1. 값이 바뀌면 `a`라는 변수의 기존값을 잃기때문에, `b`라는 자리에는 임시변수를 부여하여 `a`를 할당해준다.
2. 또는 구조분해할당을 사용할 수 있다.
3. `swap`를 선언하여 `swap`가 실행된 횟수를 기록한다.
   1. `swap`이 0일 경우, 배열은 정렬되지 않은 상태이다.
4. 그 다음 반복문에서 매 반복마다 `i`번째로 큰 수가 마지막에서 `i`번째 위치하게 한다.
5. 만약 `j`가 `j+1`번째보다 크다면, `swap`함수를 인자를 전달하여 위치를 바꿔준다.
6. `for`문이 끝났을 때는 배열이 정렬된 상태이기 때문에 이를 출력한다.


```js
const swap = function (idx1, idx2, arr) {
    [arr[idx1], arr[idx2]] = [arr[idx2], arr[idx1]];
}

let bubbleSort = function (arr) {
  let N = arr.length;

  for (let i = 0; i < N; i++) {
    let swaps = 0;

    for (let j = 0; j < N - 1 - i; j++) {
      if (arr[j] > arr[j + 1]) {
        swaps++;
        swap(j, j + 1, arr);
      }
    }

    if (swaps === 0) {
      break;
    }
  }

  return arr;
};

```