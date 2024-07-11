---
layout: post
title: Javascript 32장 - this와 apply,call,bind
author: admin
date: 2024-07-11 00:00:00 +900
lastmod: 2024-07-11 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [JAVASCRIPT]
tags: [this, apply, call, bind, prototype]
---

# Javascript

- 처음부터 다시 보는 딥 다이브

## this

- 자바스크립트 엔진에 의해 자동으로 생성되며, 자신이 속한 객체 혹은 자신이 생성할 인스턴스의 프로퍼티나 메서드를 참조할 수 있다.
- this가 가리키는 값은 호출 방식에 의해 결정된다.
- `자바스크립트의 this는 호출되는 방식에 따라 동적으로 결정된다.`
-

### 객체 리터럴의 메서드 내부에서 this

- 메서드를 호출한 객체이다.

```jsx
const ex = {
  name: "Good",

  getName() {
    return `Name is ${this.name}`; // Good
  },
};
```

### 생성자 함수 내부의 this

- 생성자 함수가 생성할 인스턴스이다.

```jsx
function Name(name) {
  this.name = name;
}

Name.prototype.getName = function () {
  return `Name is ${this.name}`;
};

const person = new Name("Good");
```

### 일반 함수 내부의 this

- 전역 객체(global this)가 바인딩된다.
- 전역 함수와 중첩 함수를 일반 함수로 호출된 모든 함수 내부의 this는 전역 객체를 가리킨다.
- strict mode에서는 undefined가 바인딩된다.

```jsx
function parent() {
  this; // global

  function child() {
    this; // global
  }
}

function strictParent() {
  ("use strict");

  this; // undefined

  function strictChild() {
    this; // undefined
  }
}
```

#### 일반 함수와 중첩 함수의 호출에 따른 this

```jsx
const value = 1;

const obj = {
  value = 100,

  objFunc(){
    this.value; //

    objNestedFunc(){
      this.value; // 메서드 내 중첩 함수이기에 일반 함수 호출처럼 작동 Global value 1
    }

    objNestedFunc();
  },
}

obj.objFunc(); // 객체 내 메서드로 호출된 함수 value 100
```

```jsx
const value = 1;

const obj = {
  value = 100,

  objFunc(){
    this.value;

    setTimeout(function(){
      this.value; // callback 함수 Global
    }, 100)
  }
}

obj.objFunc(); // 객체 내 메서드로 호출된 함수 value 100
```

### apply / call / bind

- Function.prototype 의 메서드이다.
- 모든 함수가 이 메서드들을 상속받아 사용할 수 있다.
  - `Function.prototype.apply(this로 사용될 객체, arguments 리스트(배열 or 유사배열객체))`
  - `Function.prototype.call(this로 사용될 객체, arguments 인수 리스트( , 구분하여 전달))`
  - `Function.prototype.bind(this로 사용될 객체)`

#### apply / call

- 본질적으로는 함수를 호출한다.
- 첫 번째 인수로 전달한 특정 객체를 호출한 함수의 this에 바인딩한다.
- 두 번째 인수를 함수에 전달하는 방식만 다를 뿐 동일하게 작동한다.
- arguments 객체와 같은 유사 배열 객체에 배열 메서드를 사용할 경우 효과적이다.

```jsx
function Example() {
  const arr = Array.prototype.slice.call(arguments);

  return arr;
}

Example(1, 2, 3);
// arguments는 { "0":1, "1":2, "2":3}
// 유사 배열 객체는 숫자 인덱스와 length를 갖고 있지만 slice는 갖고 있지 않다.
// slice는 새로운 배열 객체를 반환하는데, 배열과 length 숫자 인덱스를 필요로 한다.
// Array.prototype.slice.call을 사용하여 slice 메서드를 arguments 객체에 대해 호출한다.

// call을 통해 객체에 대해 호출하고 arguments의 length와 숫자 인덱스를 사용하여 배열을 생성한다.
// agruments가 배열이 아니어도 필요한 프로퍼티인 length만 있다면 정상적으로 작동한다.

// slice메서드는 내부적으로 대상 객체의 length를 확인하고, 0부터 length - 1까지의 인덱스를 순회하며
// 새로운 배열을 얻어내는데, arguments에는 이에 필요한 프로퍼티를 전부 갖고 있기 때문에 정상적으로 작동한다.

// ES6
const arr = Array.from(arguments);
const arr = [...arguments];
```

#### bind

- 함수를 호출하진 않고 this로 사용할 객체를 전달한다.
- 메서드의 this와 내부의 중첩 함수 혹은 콜백 함수의 this가 불일치하는 문제를 해결하는데 효과적이다.

```jsx
const person = {
  name: "Rang",
  callName(callback) {
    setTimeout(() => callback(), 100);
  },
};

person.callName(function () {
  `Name is ${this.name}`;
});
// 중첩 함수인 콜백함수는 전역 객체를 가리키기 때문에 this.name이 존재하지 않는다.

const person = {
  name: "Yoon",
  callName(callback) {
    setTimeout(() => callback.bind(this)(), 100);
  },
};

person.callName(function () {
  `Name is ${this.name}`;
});
// Function.prototype.bind 메서드로 콜백 함수의 주체를 person 객체로 동적 바인딩을 해준다.
// 때문에 person 객체의 name 프로퍼티에 접근할 수 있다.
```

## 느낌표

- `일반 함수 호출할 때 this는 전역 객체를 가리킨다.`
- `메서드를 호출할 때 this는 메서드를 호출한 객체를 가리킨다.`
- `생성자 함수를 호출할 때 this는 생성자 함수가 생성할 인스턴스를 가리킨다.`
- `Function.prototype에 apply,call,bind 메서드에 의한 간접 호출의 this는 apply,call,bind 메서드에 첫 번째 인자로 전달한 객체를 가리킨다.`
