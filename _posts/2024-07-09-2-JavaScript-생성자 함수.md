---
layout: post
title: Javascript 27장 - 생성자 함수와 일반 함수 객체
author: admin
date: 2024-07-09 00:00:00 +900
lastmod: 2024-07-09 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [JAVASCRIPT]
tags: [function, constructor, new, instanceof, newtarget]
---

# Javascript

- 처음부터 다시 보는 딥 다이브

## 생성자 함수

- `new 연산자 와 함께 호출하여 객체를 생성하는 함수이다.`
- 자바스크립트에서 객체를 생성하는 방법 중 하나인 객체 리터럴 에 의한 생성 방식은 직관적이고 간편하다는 장점이 있지만, 단 하나의 객체만 생성하고 구조가 동일하여도 매번 같은 프로퍼티와 메서드를 작성해야 한다.

```jsx
const personA = {
  name: "Girang",
  getName() {
    return `Name is ${this.name}`;
  },
};

const personB = {
  name: "Jiyoon",
  getName() {
    return `Name is ${this.name}`;
  },
};
```

- `생성자 함수를 작성하고 new 연산자와 함께 인스턴스를 생성한다.`

```jsx
function Person(name) {
  this.name = name;
  this.getName = function () {
    return `Name is ${this.name}`;
  };
}

const personA = new Person("Girang");
const personB = new Person("Jiyoon");
```

- `new 연산자와 함께 사용하지 않으면 일반 함수로 작동하며, 작성한 프로퍼티는 전역 객체의 프로퍼티로 등록된다.`

```jsx
function Person(name) {
  this.name = name;
  this.getName = function () {
    return `Name is ${this.name}`;
  };
}

const personA = Person("Girang");

console.log(personA); // undefined
console.log(name); // Girang
```

### 생성자 함수의 인스턴스 생성 과정

- 인스턴스를 생성하면, 생성된 인스턴스를 초기화하고 옵션 작업을 실행한다.
- 자바스크립트 엔진은 암묵적으로 인스턴스를 생성하고 인스턴스를 초기화한 후 암묵적으로 인스턴스를 반환받는다.

#### 1. 인스턴스 생성과 this 바인딩

- `암묵적으로 빈 객체가 생성된다.`
- 생성된 빈 객체는 아직은 미완성된 생성자 함수가 생성한 인스턴스이다.
- `빈 객체는 this에 바인딩(식별자와 값을 연결하는 과정)된다.`
- `이 처리 과정은 코드가 한 줄씩 실행되는 런타임 이전에 실행한다.`

```jsx
function Person(name) {
  console.log(this); // Person {}

  this.name = name;
  this.getName = function () {
    return `Name is ${this.name}`;
  };
}
```

#### 2. 인스턴스 초기화

- 생성자 함수가 기술되어 있는 코드를 한 줄씩 실행시키면서 `this에 바인딩되어 있는 인스턴스를 초기화한다.`
- 인스턴스에 프로퍼티나 메서드를 추가하고 생성자 함수가 인수로 전달받은 초기값을 인스턴스 프로퍼티에 할당하여 초기화하거나 고정값을 할당한다.

```jsx
function Person(name) {
  // this에 바인딩되어 있는 인스턴스를 초기화한다.
  this.name = name;
  this.getName = function () {
    return `Name is ${this.name}`;
  };
}
```

#### 3. 인스턴스 반환

- `생성자 함수의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.`

```jsx
function Person(name) {
  this.name = name;
  this.getName = function () {
    return `Name is ${this.name}`;
  };
}

// 인스턴스를 생성하고 Person 생성자 함수는 암묵적으로 this(Person 인스턴스)를 반환한다.
const person = new Person("Girang");
```

- `바인딩 된 this를 암묵적으로 반환하는 것 대신, 다른 객체를 명시적으로 반환할 경우, return문에 명시한 객체가 반환된다.`
- 명시적으로 원시값을 반환할 경우, 원시값은 무시되고 암묵적으로 this가 반환된다.

```jsx
function Person(name) {
  this.name = name;
  this.getName = function () {
    return `Name is ${this.name}`;
  };

  return {};
}

const person = new Person("Girang");
console.log(person); // Person {} - return문에 명시한 객체
```

```jsx
function Person(name) {
  this.name = name;
  this.getName = function () {
    return `Name is ${this.name}`;
  };

  return 100;
}

const person = new Person("Girang");
console.log(person); // Person {name: "Girang", getName: [Function(anonymous)]}
```

## 내부 메서드 [[call]]과 [[construct]]

- 함수와 일반 객체 모두 객체이지만, 함수는 호출이 가능한 반면 일반 객체는 호출이 불가능하다.
- `함수 객체는 일반 객체가 가지고 있는 내부 슬롯과 내부 메서드는 물론 함수 객체만을 위한 [[Environment]],[[FormalParameters]] 등의 내부 슬롯과 [[Call]],[[Cinstruct]] 같은 내부 메서드를 추가로 가지고 있다.`
- `일반 함수로 호출되면 [[Call]]이 호출되고 생성자 함수로 호출되면 [[Construct]]가 호출된다.`
- `[[Construct]]를 갖는 객체를 constructor, 갖지 않는 객체를 non-constructor라고 한다.`
- [[Call]]를 갖는 객체를 callable이라고 한다.

### constructor와 non-constructor

- `constructor : 생성자 함수로 호출할 수 있는 형태의 함수 선언문, 함수 표현식`
- `non-constructor : 생성자 함수로 호출할 수 없는 형태의 화살표 함수, 메서드(ES6 메서드 축약 표현)`
- 모든 함수 객체는 반드시 내부 메서드 [[Call]]를 가지고 있다.
- 모든 함수 객체가 [[Construct]]를 가지고 있는 건 아니다.
- 모든 함수 객체는 callable 이지만, constructor이거나 non-constructor이다.
- 모든 함수 객체를 생성자 함수로 호출할 수 있는 건 아니다.

## new 연산자

- new 연산자와 함께 함수를 호출하면 해당 함수가 생성자 함수로 동작한다.
- 함수 객체 내부의 메서드 중 [[Construct]]가 호출되는 것이다.

```jsx
function Person() {
  this.name = name;
  this.getName = function () {
    return `Name is ${this.name}`;
  };
}

const person = new Person("Girang"); // Person {name: "Girang", getName: [Function...]}
const personNon = Person("Girang"); // undefined
```

### new.target

- ES6에 도입되어, `new 연산자와 함께 생성자 함수로써 호출되어 있는지 확인할 수 있는 문법`이다.
- `new 연산자와 함께 생성자 함수로써 호출되면 함수 내부의 new.target은 자기 자신을 가리킨다.`
- `new 연산자 없이 일반 함수로 호출된 함수 내부의 new.target은 undefined를 가리킨다.`

```jsx
function Person(name){
  if(!new.taget){
    return new Person(name);
  }

  this.name = name;
  this.getName = function(){
    return `Name is ${this.name}`;
  }
}

const person = Person("Girang"); // Person {...}
// new.target으로 인해, 생성자 함수로 호출된 것이 아니기에 false, 재귀 함수로 생성자 함수로 호출된다.
```

### 스코프 세이프 생성자 패턴

- `new.target`은 IE와 같은 브라우저에서 지원하지 않기 때문에, 이러한 환경에서 생성자 함수를 보장해야 한다.
- `instanceof`를 사용하여 객체가 특정 클래스에 속한지를 확인하는데, 생성자 함수로 생성되지 않은 경우 this는 전역 객체인 window를 가리킨다.

```jsx
function Person() {
  if (this instanceof Person) new Person();

  this.name = name;
  this.getName = function () {
    return `Name is ${this.name}`;
  };
}
```
