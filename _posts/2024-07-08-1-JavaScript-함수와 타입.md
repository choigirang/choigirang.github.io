---
layout: post
title: Javascript 24장 - 함수와 타입
author: admin
date: 2024-07-08 00:00:00 +900
lastmod: 2024-07-08 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [JAVASCRIPT]
tags: [string, tostring, function, arrow, arguments]
---

# Javascript

- 처음부터 다시 보는 딥 다이브

## 타입 변환

### 문자열 변환

- 문자열로 타입을 바꾸는 방법은 크게 2가지가 있다.

#### String

- 전역 객체의 메서드로 모든 타입의 값을 문자열로 변환할 수 있다.
- `null, undefined`를 문자열로 변환하는, 일반적으로 사용하는 방법이다.

#### toString

- 프로토타입의 메서드로 `null, undefined`를 직접 호출 시 에러를 발생시킨다.

##### 메서드 차이

- 전역 객체 메서드는 자바스크립트 환경의 전역 객체에 속한다. `(브라우저의 window, Node.js의 global 등)`
- 프로토타입 메서드는 각 객체 타입의 프로토타입에 정의된다.
- 전역 객체 메서드는 주로 난수 생성(Math...), NaN 여부 확인(isNaN...) 등의 범용적인 유틸리티 기능을 제공하며, 프로토 타입 메서드는 특정 타입에 대해 특화된 작업을 수행한다.(push, split...)
- 전역 객체의 메서드는 전역 스코프에서 직접 사용할 수 있는 반면, 프로토타입 메서드는 해당 타입의 인스턴스를 통해 호출해야 한다.
  ```jsx
  // 전역
  String(1);
  // Obeject.prototype.toStirng
  (1).toString();
  ```

### 숫자 변환

- `Number, parseInt, parseFloat...` 함수
- 산술 연산자 사용 `+"0"`

### 논리합과 논리곱

- 논리곱 `&&` 두 번째 피연산자가 결정, 논리합 `||` 첫 번째 피연산자가 결정한다.

```jsx
"Apple" && "Banana"; // Banana
"Apple" || "Banana"; // Apple
```

## 객체

### 메서드 축약 표현

```jsx
// es5
const es5 = {
  sayHi: function () {},
};

// es6
const es6 = {
  sayHi() {},
};
```

- 프로퍼티 할당 함수는 `생성자 함수로 사용 가능`, 메서드 축약은 사용 불가
- 프로퍼티 할당 함수는 `prototype 속성 존재`, 메서드 축약은 속성 없음
- 프로퍼티 할당 함수 `super 키워드 사용 불가`, 메서드 축약은 사용 가능

## 함수

- 함수를 정의하는 방법은 크게 4가지가 있다.

```jsx
// 함수 선언문
function method1() {}

// 함수 표현식
const method2 = function () {};

// 화살표 함수 es6
const method3 = () => {};

// new Function 생성자 함수
const method4 = new Function();
```

### 화살표 함수와 생성자 함수

- this 바인딩이 동적과 렉시컬로 나뉜다.
  - 화살표 함수는 함수 정의 시점에 결정되는 되는 렉시컬 this 바인딩이다.
  - 생성자 함수는 함수 호출 시점에 결정되는 동적 this 바인딩이다.
- arguments가 객체 생성 여부가 나뉜다.

  ```jsx
  // 생성자 함수 arguments 유사 배열 객체 형태 자동 생성
  function method1() {
    return arguments;
  }

  method1(1, 2, 3); // {0 : 1, 1: 2, 2: 3}

  // 화살표 함수
  const method2 = () => arguments; // ReferenceError
  ```

- 가변 인자 처리 혹은 ...rest 형태를 사용한다.

  ```jsx
  // 생성자 함수
  function Method1(name, age) {
    this.name = name;
    this.age = age;
    this.info = Array.from(arguments);

    for (let i = 1; i <= arguments.length; i++) {
      this["favorite" + i] = arguments[i];
    }
  }

  const methodEx = new Method1("Girang", 27, "work out", "food");
  // Method {
  //    name: "Girang",
  //    age : 27,
  //    info : ["Girang", 27, "work out", "food"]
  //    favorite1 : "work out",
  //    favorite2 : "food",
  // }

  // 화살표 함수
  const method2 = (...arguments) => {
    return {
      name: arguments[0],
      age: arguments[1],
      info: [...arguments],
      favorite1: arguments[2],
      favorite2: arguments[3],
    };
  };

  method2("Girang", 27, "work out", "food"); // 위와 동일
  ```

- 자체 arguments를 가지거나 상위 스코프의 arguments를 참고할 수 있다.

  ```jsx
  function method1() {
    function inner() {
      return arguments;
    }
    inner();
  }

  function method2() {
    const inner = () => {
      return arguments;
    };
    inner();
  }

  method1("Girang", "Hi"); // 참고 불가 {}
  method2("Girang", "Bye"); // {0: "Girang", 1: "Bye"}
  ```

- 생성자 함수는 자체 arguments 객체 생성으로 인한 오버헤드가 발생하는 것과 달리 화살표 함수는 오버헤드가 발생하지 않는다.

### 느낌표

- 함수 이름은 함수 몸체 내에서 참조할 수 있는 식별자이다.
- 함수 리터럴 표현식은 함수 이름으로 참조할 수 없다는 것을 의미하는데, 함수를 가리키는 식별자가 없다는 의미이다.
- `함수 선언문은, 함수 선언문을 해석해 함수 객체를 생성한다.`
- `자바스크립트 엔진은 생성된 함수를 호출하기 위해 함수 이름과 동일한 이름의 식별자를 생성하고, 거기에 함수 객체를 할당한다.`
- 함수는 함수 이름으로 호출하는 것이 아니라 함수 객체를 가리키는 식별자를 호출하는 것을 의미한다.
- `자바스크립트 엔진은 함수 선언문을 함수 표현식으로 변환, 함수 객체를 생성한다.`
- 인수가 매개변수보다 많으면 암묵적으로 arguments 에 보관된다.
