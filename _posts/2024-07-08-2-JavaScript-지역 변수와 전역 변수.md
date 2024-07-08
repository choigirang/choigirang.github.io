---
layout: post
title: Javascript 25장 - 지역 변수와 전역 변수
author: admin
date: 2024-07-08 00:00:00 +900
lastmod: 2024-07-08 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [JAVASCRIPT]
tags: []
---

# Javascript

- 처음부터 다시 보는 딥 다이브

## 변수와 생명 주기

### 지역 변수 생명 주기

- 지역 변수의 생명 주기는 함수의 생명 주기와 일치한다.

  ```jsx
  function method1() {
    var x = "local";
    console.log(x);
    return x;
  }

  method1(); // local
  console.log(x); // ReferenceError
  ```

- 지역 변수의 호이스팅은 지역 변수의 선언이 스코프의 선두로 끌어올려지는 것처럼 동작한다.

  ```jsx
  var x = "global";

  function method1() {
    console.log(x); // undefined
    var x = "local";
  }

  method1();
  console.log(x); // global
  ```

  - method 함수의 변수는 함수 호출 시 `호이스팅으로 인해 식별자가 등록되어 있지만, 값할당이 이루어지지 않았기 때문에 Error가 아닌 undefined가 발생`한다.
  - 전역 변수로 사용된 x는 함수 내부에서 선언된 x에게 유효하지 않기 때문에 함수 호출 전의 console 메세지에 출력되지 않는다.

### 전역 변수 생명 주기

- 전역 변수의 생명 주기는 전역 객체의 생명 주기와 일치한다.
- 전역 객체란, 코드가 실행되기 이전에 자바스크립트 엔진에 의해 어떤 객체보다도 먼저 생성되는 특수한 객체이다.
- 브라우저에서는 window, Node.js에서는 global 객체를 의미한다.
- ES11에서 globalThis 로 전역 객체를 가리키는 식별자를 통일했다.
- `표준 객체(Object, String, Number, Function, Array...)와 환경에 따른 호스트 객체(브라우저의 WebAPI 또는 Node.js의 호스트 API), var 키워드로 선언한 전역 변수와 전역 함수를 프로퍼티로 가진다.`
- 전역 코드는 명시적인 호출 없이 실행되며, 로드되자마자 곧 바로 해석되고 실행된다.
- `함수는 함수 내의 마지막 문이 실행되면 종료되지만, 전역 코드는 반환문이 없으므로 마지막 문이 실행되어 더 이상 코드가 없을 때까지 존재한다.`

#### 전역 변수의 문제점

- 코드 내 `어디서든 참조하고 할당하고 사용할 수 있다는 암묵적인 결합을 가진다.`
- 변수의 유효 범위가 클수록 코드의 가독성이 나빠지고, 의도치 않게 상태가 변경될 수 있다는 위험도를 증가시킨다.
- `전역 변수는 프로그램이 종료되기 전까지 메모리 공간에 할당되어 해제되지 않기 때문에 메모리 리소스를 오래 사용한다.`
- 변수를 검색할 때 `전역 변수가 가장 마지막에 검색 된다는 것을 의미하여 전역 변수의 검색 속도가 가장 느리다.`
- 자바스크립트의 큰 문제점 중 하나인, `파일이 분리되어있어도 하나의 전역 스코프를 공유하기 때문에 동일한 이름의 변수나 전역 함수가 같은 스코프 내에 존재할 경우 예상치 못한 결과를 초래한다.`

#### 전역 변수의 사용을 억제하기 위한 방법

- 즉시 실행 함수를 사용하여, 생명 주기가 종료될 수 있도록 한다.
  ```jsx
  (function () {
    var example = 100;
  })(); // 즉시 실행 함수와 지역 변수
  ```
- 네임스페이스 객체(담당할 객체를 생성하고 전역 변수처럼 사용하고 싶은 변수를 프로퍼티로 추가하는 방법)를 사용한다.

  - 네임스페이스 객체 자체가 전역 변수에 할당되므로 선호되진 않는다.

  ```jsx
  var METHOD = {};

  METHOD.print = {
    sayHello() {
      console.log("Hello");
    },
  };
  ```

- 클래스를 모방한 모듈 패턴을 사용하여 관련이 있는 변수와 함수를 모아 즉시 실행 함수로 감싸 하나의 모듈을 만들어 사용한다.

  - 자바스크립트의 강력한 기능인 클로저 패턴을 기반으로 동작하여 전역 변수의 억제는 물론 캡슐화(정보 은닉)까지 가능하다.

  ```jsx
  var METHOD = (function () {
    // 외부로 공개되지 않는다.
    var sayHello = "Hello";

    // return 문의 데이터나 메서드 프로퍼티는 외부에 공개된다.
    return {
      print() {
        console.log("Hello");
      },
      printErr() {
        console.log(new Error());
      },
    };
  })();

  console.log(sayHello); // undefined
  ```
