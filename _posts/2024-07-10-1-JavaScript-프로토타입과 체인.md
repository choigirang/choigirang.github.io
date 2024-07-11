---
layout: post
title: Javascript 30장 - 프로토타입과 체인
author: admin
date: 2024-07-10 00:00:00 +900
lastmod: 2024-07-10 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [JAVASCRIPT]
tags: [prototype, chain, overiding]
---

# Javascript

- 처음부터 다시 보는 딥 다이브

## 리터럴 객체와 생성자 함수의 프로토타입

- `리터럴 표기법으로 생성된 객체의 프로토타입인 경우, constructor 프로퍼티가 가리키는 생성자 함수가 객체를 생성한 생성자 함수가 아닐 수 있다.`

  ```jsx
  const ex = {};

  ex.constructor === Obeject; // true
  ```

### ECMAScript의 추상연산 호출에 의한 객체 생성

- `OrdinaryObjectCreate 를 호출하면 기본적으로 Object.prototype 을 프로토타입으로 갖는 빈 객체를 생성한다.`
- Object 생성자 함수 호출과 객체 리터럴의 평가는 OrdinaryObejectCreate를 호출해서 빈 객체를 생성한다는 점은 동일하다.
  - 다만 new.target 의 확인, 프로퍼티 추가 처리 등 세부 처리에서 차이가 있다.
  - 객체 리터럴에 의해 생성된 객체 !== Object 생성자 함수가 생성한 객체
- 프로토타입과 생성자 함수는 언제나 쌍으로 존재한다.

  - 객체 리터럴 : Object 생성자 함수 - Object.prototype
  - 함수 리터럴 : Function 생성자 함수 - Function.prototype
  - 배열 리터럴 : Array 생성자 함수 - Array.prototype
  - 정규 표현식 리터럴 : RegExp 생성자 함수 - RegExp.prototype

##### 프로토타입의 생성 시점

- `프로토타입은 생성자 함수가 생성되는 시점에 생성된다.`
- 생성자 함수는 사용자 정의 생성자 함수와 자바스크립트가 기본 제공하는 빌트인 생성자 함수로 구분된다.
- `생성자 프로토타입은 오직 constructor 프로퍼티만을 갖는 객체이다.`
- 프로토타입도 객체이며 모든 객체는 프로토타입을 가진다.
- `프로토타입도 자신의 프로토타입을 가지며 생성된 프로토타입의 프로토타입은 Object.prototype이다.`

```jsx
// 함수 호이스팅 적용, 생성자 함수 행성과 동시에 프로토타입 생성
console.log(Example.prototype); // {constructor: f}

function Exampe(){
  ...
}
```

##### 빌트인 생성자 함수와 프로토타입 생성 시점

- 빌트인 생성자 함수는 빌트인 생성자 함수가 생성되는 시점에 프로토타입이 생성된다.
- 모든 빌트인 생성자 함수는 전역 객체가 생성되는 시점에 생성된다.
- 생성된 프로토타입은 빌트인 생성자 함수의 prototype 프로퍼티에 바인딩된다.

#### 객체 생성 방식과 프로토타입 결정

1. 객체 리터럴
2. Object 생성자 함수
3. 생성자 함수
4. Object.create 메서드
5. 클래스 (ES6)

- 다양한 방식의 차이가 있는 객체 생성 방식이지만, 추상 연산 OrdinaryObjectCreate 호출에 의해 객체가 생성된다는 것은 동일하다.
- OrdinaryObjectCreate 호출로 빈 객체를 생성하고, 객체에 추가될 프로퍼티 목록이 인수로 전달될 경우 프로퍼티 객체에 추가된다.
- 인수로 전달받은 프로토타입을 자신이 생성한 객체의 [[Prototype]] 내부 슬롯에 할당한 경우 생성한 객체를 반환한다.

##### 객체 리터럴에 의해 생성된 객체의 프로토타입

- `객체 리터럴에 의해 생성된 객체의 프로토타입은 Object.prototype`

```jsx
// 객체 리터럴에 의해 생성된 ex 객체
const ex = { value: "값" };

// ex 객체에는 constructor 프로퍼티와 hasOwnProperty 메서드를 소유하지 않는다.
// 그럼에도 사용가능한 것은 Object.prototype 에 있는 프로퍼티를 상속받았기 때문이다.
ex.constructor === Object; // true
ex.hasOwnProperty("value"); // true
```

##### Object 생성자 함수에 의해 생성된 객체의 프로토타입

- Object 생성자 함수에 의해 생성되는 객체의 프로토타입은 Object.prototype

```jsx
const ex = new Object();
ex.value = "값";

console.log(ex.constructor === Object); // true
console.log(ex.hasOwnProperty("value")); // true
```

##### 생성자 함수에 의해 생성된 객체의 프로토타입

- 생성자 함수에 의해 생성된 객체는 생성자 함수의 prototype 프로퍼티에 바인딩되어 있는 객체
- `Object 생성자 함수와 객체 리터럴로 생성된 객체의 프로토타입은 Object.prototype과 달리, 오직 constructor 프로퍼티만 존재한다.`

```jsx
function Example(){
  ...
}

Example.prototype.sayHi = function(){
  console.log("Hi");
}

const me = new Example();
const you = new Example();

me.sayHi(); // Hi
you.sayHi(); // Hi

me.constructor === Example; // true
you.constructor === Example; // true
```

### 프로토타입 체인

- `프로토타입의 프로토타입과 프로토타입 체인은 언제나 Object.prototype 이다.`
- 자바스크립트는 객체의 프로퍼티(메서드 포함)에 접근하려고 할 때, 해당 객체에 접근하려는 프로퍼티가 있는지 확인한다.
- `없다면 [[Prototype]] 내부 슬롯의 참조값을 따라, 자신의 부모 역할을 하는 프로토타입의 프로퍼티를 순차적으로 검색한다.`
- 이를 프로토타입 체인이라 하며, 자바스크립트 객체 지향 프로그래밍의 상속을 구현하는 매커니즘이 된다.
- 모든 객체는 언제나 Object.prototype을 상속받으며, 최상위이기 때문에 Object.prototype의 프로토타입은 없다(null).

#### 프로토타입 체인 / 스코프 체인

- 프로토타입 체인
  - 자바스크립트 엔진은 프로토타입 체인에 따라 프로퍼티와 메서드를 검색
  - 객체 간의 상속 관계로 이루어진 프로토타입의 계층적인 구조에 따라 프로퍼티를 검색
  - 프로토타입 체인은 상속과 프로퍼티 검색을 위한 매커니즘
- 스코프 체인
  - 자바스크립트 엔진은 함수의 중첩 관게로 이뤄진 스코프의 계층적인 구조에서 식별자를 검색
  - 스코프 체인은 식별자 검색을 위한 매커니즘
- 프로토타입 체인과 스코프 체인은 별도로 작동하는 것이 아닌, 서로 협력하여 식별자와 프로퍼티를 검색하는데 사용된다.
- 스코프 체인 => 프로토타입 체인 순

### 오버라이딩 / 프로퍼티 섀도잉

- 동일한 이름의 프로퍼티에 대해
  - 인스턴스의 메서드가 프로퍼티의 메서드를 덮어쓰는 것을 오버라이딩이라고 한다.
  - 프로토타입의 메서드가 인스턴스의 메서드로 인해 가려지는 것을 섀도잉이라고 한다.
- 프로토타입이 소유한 프로퍼티 => 프로토타입 프로퍼티
- 인스턴스가 소유한 프로퍼티 => 인스턴스 프로퍼티

```jsx
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  Person.prototype.say = function () {
    console.log(`Hi, ${this.name}`);
  };

  return Person;
})();

const me = new Person("Rang");
me.say(); // Hi, Rang

// 인스턴스에 정의
me.say = function () {
  console.log(`Bye, ${this.name}`);
};
// Person.prototype의 say가 아닌, 인스턴스의 say 호출
me.say(); // Bye, Rang
```

- 프로퍼티 오버라이딩을 통해 프로토타입에 프로퍼티를 추가하는 것은 가능하나, 프로퍼티를 변경/삭제 할 수 없다.

```jsx
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  Person.prototype.say = function () {
    console.log(`Hi, ${this.name}`);
  };

  return Person;
})();

const me = new Person("Rang");
me.say(); // Hi, Rang

// 인스턴스에 정의
me.say = function () {
  console.log(`Bye, ${this.name}`);
};

delete Person.prototype.say;
me.say(); // Bye, Rang
Person.prototype.say(); // TypeError
```

### 프로토타입 교체

- 프로토타입은 다른 객체로 변경될 수 없고 부모 객체인 프로토타입을 동적으로 변경할 수 있다.
- 프로토타입 교체에 의해 객체간의 상속 관계를 동적으로 변경하는 것은 번거로우며, 직접 프로토타입을 교체하는 것은 바람직하지 않다.
- 직접 상속이나 ES6 + 의 클래스를 사용

#### 생성자 함수에 의한 프로토타입 교체

```jsx
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  Person.prototype = {
    // constructor 프로퍼티와 생성자 함수 간의 연결을 재설정하면 파괴를 매꿀 수 있다.
    // constructor: Person,
    sayHello() {
      console.log(`Hi, My name is ${this.name}`);
    },
  };

  return Person;
})();

const me = new Person("WI");
// 생성자 함수에 프로퍼티로 프로토타입을 교체하면 constructor 프로퍼티와 생성자 함수 간의 연결이 파괴
console.log(me.constructor === Person); // false
// 프로토타입 체인을 따라 Object.prototype 의 constructor 프로퍼티가 검색
console.log(me.constructor === Object); // true
```

#### 인스턴스에 의한 프로토타입 교체

```jsx
function Person(name) {
  this.name = name;
}

const me = new Person("WI");

const parent = {
  // 생성자 함수에 의한 프로토타입 재정의 때와 같이 constructor가 파괴되는 것을 constructor 를 해당 생성자 함수로 재설정하면 매꿀 수 있다.
  // constructor: Person,
  sayHello() {
    console.log(`Hi, My name is ${this.name}`);
  },
};

Object.setPrototypeOf(me, parent);
console.log(me.constructor === Person); // false
console.log(me.constructor === Object); // true
```
