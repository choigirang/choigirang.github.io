---
layout: post
title: JavaScript 7장 - 개념짚기 (1)
author: admin
date: 2023-06-14 00:00:00 +900
lastmod: 2022-06-14 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [JAVASCRIPT]
tags: [javascript, js, function, switch]
---

# JavaScript

## 자료형

- `typeof null`은 `Object` 객체형으로 표현된다. `null`은 객체가 아니지만 자바스크립트의 초기 버전의 오류로 하위 호환성을 위해 수정되고 있지 않다.

## alert, prompt, confirm

### alert

- 사용자에게 확인창을 보여준다.
- `alert("링크로 이동합니다")`

- 추가적인 이벤트 설정도 가능하다.
- `if(alert("링크로 이동합니다")) document.location = "http://exampleLink.com"`

### prompt

- `alert, confirm`과 비슷하게 사용자가 입력할 수 있는 입력창이 생성된다.
- 두 개의 입력값을 받을 수 있는데 함수의 첫 번째 인수에는 타이틀을 나타내고, 두 번째 인수에는 사전에 들어갈 기본 입력값이 들어간다.
- 입력받은 값은 무조건 문자열로 표현되기 때문에 사용자가 입력한 값을 활용하고자 한다면 숫자형으로 변환하여 사용해야 한다. => `Number()`
- `prompt("예약일을 입력해주세요", "2020-10-")`

![예시](https://github.com/choigirang/none.github.io/assets/118104644/ce39a09e-ef9e-45fd-a47c-83ee29946705)

### confirm

- 사용자에게 선택창을 보여준다.
- 스크립트가 일시정지되고 스타일링이 불가능하다는 단점이 있다. => 대체 라이브러리 존재
- `confirm("링크로 이동하시겠습니까?")`

- 마찬가지로 추가적인 이벤트 설정이 가능하다.

```js
if(confirm("링크로 이동하시겠습니까?")document.location = "https://github.com/choigirang")
```

## 형변환

- 문자 90과 문자 80을 더하면 `9080`이 되지만 곱하기,나누기 등의 수식은 **자동 형변환**에 의해 올바르게 계산된다. => `"100"/2 = 50`
- 이는 오류를 발생시킬 수 있어 의도를 갖고 원하는 타입으로 변환해주어야 하며 이를 **명시적 형변환** 이라고 한다.
- `Number(), String(), Boolean()`

```js
Number(0); // null
Number(undefined); // NaN

Boolean(1); // true
Boolean("1"); // true
Boolean(0); // false
Boolean(""); // false
Boolean(" "); // true
```

## 반복문

- 특정 조건을 만족할 때까지 반복문이 실행된다.
- `for`, `while`, `do..while`등이 있다.
- `while(true)`는 무한 반복하기 때문에 주의해야 한다.
- `do...while`문은 `while`과 비슷한데 조건문을 아래로 옮길 수 있다.
  - `while`에 작성된 조건문까지 `do`의 코드를 실행한다.
  - `do`에 작성된 코드를 최소 한 번은 실행한다는 것에서 `while`과 큰 차이가 있다.
- `break`를 사용해서 반복문을 빠져나올 수 있고, `continue`는 반복문을 멈추고 다음 반복으로 진행된다.

```js
for (let i = 0; i < 10; i++) {
  if (i % 2) continue;
  console.log(i);
}

while (true) {
  let answer = confirm("계속 할까요?");
  if (!answer) break;
}

do {
  // 실행될 코드
  i++;
} while (i < 10);
```

## switch문

- `if`와 동일하게 사용되지만 조건이 많을 경우 `switch`문을 사용하면 보다 간결하게 작성할 수 있다.

```js
switch (평가) {
  case A: //A일 때 코드
  case B: //B일 때 코드
}

if (평가 == A) {
  // A일 때 코드
} else if (평가 == B) {
  // B일 때 코드
}
```

- `case`에 관한 코드가 실행되고 아래에 `case`에 관한 조건도 확인하여 작동되기 때문에 `break`를 넣어 나머지 코드가 실행되지 않게 해주어야 한다.

```js
const example = prompt("무슨 과일을 드시겠습니까?");

switch (example) {
  case "사과":
    console.log("100원");
    break;
  case "바나나":
    console.log("200원");
    break;
  case "멜론":
    console.log("300원");
    break;
}
```

- 만약 어떤 `case`에도 해당하지 않을 경우 `switch`문을 그대로 빠져나오기 때문에 이에 대한 기본값을 설정해주어야 한다.

```js
const example = prompt("무엇을 준비해드릴까요?");

switch (example) {
  case "음료":
    console.log("음료수 메뉴판입니다.");
    break;
  case "피자":
    console.log("피자 메뉴판입니다.");
    break;
  default:
    console.log("메뉴판을 다시 찾아보겠습니다.");
    break;
}

// 입력값 : 햄버거
// console : 메뉴판을 다시 찾아보겠습니다.
```

## 함수

- 함수 선언문과 함수 표현식이 있다.
- 함수 선언문은 함수를 어디서든 사용할 수 있다는 큰 차이점이 존재한다.
- 자바스크립트는 차례대로 실행되기 때문에 첫 번째 줄에서 작성된 `console.log()`가 두 번째 줄에 작성된 코드를 참조하고 있다면, 첫 번째 코드에서는 두 번째 코드를 확인할 수 없어 동작하지 않는다.
- 허나 함수 선언문으로 작성하면 첫 번째 줄에서 함수를 호출하고 두 번째 줄에 함수를 작성해도 실행된다.
- **자바스크립트는 코드가 실행되기 전 초기화 단계에서 선언된 함수 선언문을 미리 찾아서 생성해두기 때문에 가능하며, 이를 호이스팅(hoisting)이라고 한다.**
- 함수 표현식은 선언문과 달리 코드에 도달했을 때에 실행되기 떄문에 호출 위치에서 큰 차이점이 있다.

### 화살표 함수

- 화살표 기호를 사용하여 리턴문과 중괄호를 생략할 수 있다.

```js
const exampleFunc = function () {
  return "Hi";
};

// 화살표 함수
const exampleFunc = () => {
  return "Hi";
};
```

- 중괄호가 아닌 일반 괄호 사용 시 `return`을 생략할 수 있으며, 인수가 하나일 시 일반 괄호도 생략할 수 있다.
- 인수가 없다면 괄호를 생략할 수 없으며, 코드가 한 줄일 때도 화살표 기호 다음의 일반 괄호를 삭제할 수 있다.

```js
const exampleFunc = () => {
  return "Hi";
}

const exampleFunc = () => (
  "Hi";
)
```
