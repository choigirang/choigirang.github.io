---
layout: post
title: Algorithm 4장 - isSubsetOf
author: admin
date: 2023-02-22 00:00:00 +900
lastmod: 2023-02-22 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [ALGORITHM]  # 대문자로 작성
tags: [algorithm]
---
# isSubsetOf
## 문제
- 두 개의 배열(`base`,`sample`)을 입력받아 `sample`이 `base`의 부분집합인지 여부를 리턴한다.



```js
let isSubsetOf = function(base,sample){
    
}
```

## 입력
- base
  - `number` 타입을 요소로 갖는 배열
  - `base.lenght`는 100 이하
- sample
  - `number` 타입을 요소로 갖는 배열
  - `sample.length`는 100 이하


## 출력
- `boolean` 타입을 리턴한다.

## 주의사항
- `base`,`sample` 내에 중복되는 요소는 없다.

## 입출력 예시
```js
let base = [1,2,3,4,5]
let sample = [1,3]
let output = isSubsetOf(base, sample);
console.log(output)
// true

sample = [6,7]
let output = isSubsetOf(base, sample);
console.log(output)
// false

base = [10,99,123,7]
sample = [11,100,99,123]
let output = isSubsetOf(base, sample);
console.log(output)
// false
```

## 풀이
1. 각 배열을 오름차순으로 정렬한다.
2. 두 번째 `for`문에 각 `sample[i]`와 배열`base`, 처음엔 0번째 인덱스를 `findItemInSortedArr` 함수에 전달한다.
3. 전달받은 인자로 `item`은 `sample[i]`가 되고 `arr`는 `base`, `from`은 0이 된다.
   1. `baseIdx`의 초기값은 0이기 때문이다.
4. `findItemInSortedArr`의 `for`문을 돌며 `sample`의 요소와 `base`의 요소(`i`는 전달인자로 넘겨받은 `sample`의 `i`번째 인덱스)가 일치하면 `sample[i]`를 리턴하고, 리턴한 `sample[i]`는 `baseIdx`가 된다.
   1. `base`배열과 `sample[i]`를 비교하였는데 일치하는 값이 없다면 -1을 리턴한다.
5. 만약 일치한다면 `baseIdx`는 일치한 `sample[i]`가 되고, 다시 `findItemInSoredArr`함수를 돈다.
6. 넘겨받은 전달인자 `from`이 `sample[i]`이기 때문에 `i`는 넘겨받은 `sample[i]`값으로 시작하게 된다.
7. `arr=base`이기에 `base`의 `sample[i]`번째 인덱스부터 배열의 끝까지 값 중 일치하는 값이 없다면 -1을 리턴한다.
8. 배열을 전부 돌았을 때, `sample`의 요소 중 하나라도 `base`의 요소들과 일치하지 않으면 최종 `baseidx`는 -1값을 갖게 되며 그대로 `false`를 리턴하게 된다.
9. 만약 값이 있다면 두 번째 `for`문의 `if`문에 걸리지 않으므로 `true`를 리턴하게 된다.



```js
const isSubsetOf = function (base, sample){
    base.sort((a,b)=> a - b);
    sample.sort((a,b)=> a - b);

    const findItemInSortedArr = (item, arr, from) => {
        for(let i = from; i < arr.length; i++){
            if(item === arr[i]) return i;
        }

        return -1;
    }

    let baseIdx = 0;
    for(let i = 0; i < sample.length; i++){
        baseIdx = findItemInSoredArr(sample[i], base, baseIdx);
        if(baseIdx === -1) return false
    }
    return true;
}
```