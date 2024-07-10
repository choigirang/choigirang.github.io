---
layout: post
title: Javascript 29장 - 프로토타입과 프로퍼티
author: admin
date: 2024-07-10 00:00:00 +900
lastmod: 2024-07-10 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [JAVASCRIPT]
tags: [proto, prototype, constructor, property]
---

# Javascript

- 처음부터 다시 보는 딥 다이브

## 객체 지향 프로그래밍

- 원시 타입을 제외한 나머지 값들(함수, 배열, 정규표현식)은 모두 객체다.
- 자바스크립트만의 프로토타입 기반의 객체지향 프로그래밍이 존재한다.

### 추상화

- `객체의 다양한 속성 중에서 프로그램에 필요한 속성만 간추려 내어 표현하는 것을 말한다.`
- `속성을 통해 여러 개의 값을 하나의 단위로 구성한 복합적인 자료구조를 객체라고 표현한다.`
- `객체는 크게 2가지 요소로 구성되는데 상태를 나타내는 프로퍼티, 상태를 조작하는 행동을 표현하는 메서드로 구성된다.`
- 다른 말로 상태 데이터와 동작을 하나의 논리적인 단위로 묶은 복합적인 자료구조를 말한다.

```jsx
const circle = {
  // 상태
  radius: 5,
  // 행위
  getDiameter() {
    return 2 * this.radius;
  },
};
```

### 상속과 프로토타입

- 어떤 객체의 프로퍼티나 메서드를 다른 객체가 상속받아 그대로 사용할 수 있다.
- 자바스크립트는 프로토타입을 기반으로 상속을 구현하여 불필요한 중복을 제거하여 재사용성을 높일 수 있다.

```jsx
function Circle(radius) {
  this.radius = radius;
}

Circle.prototype.getArea = function () {
  return Math.PI * this.radius ** 2;
};

const circle1 = new Circle(1);
const circle2 = new Circle(2);

circle1.getArea === circle2.getArea; // true
```

#### 프로토타입

- 위의 예제에서 `getArea 메서드는 Circle 객체의 생성자 함수에 메서드로 등록된 것이 아니라 Circle 생성자 함수의 prototype 객체에 메서들로 등록된 것이다.`
- 생성자 함수에 메서드로 등록할 경우, 각각의 객체에서 독립적인 동일한 기능을 하는 메서드가 할당되어 메모리 소모가 증가한다.
- prototype에 메서드로 등록할 경우, 동일한 기능을 하는 하나의 메서드를 여러 객체가 공유하며 독립된 상태로 관리할 수 있기 때문에 메모리 소모가 절약된다.
- `모든 객체는 [[Prototype]]이라는 내부 슬롯을 가지며, 이는 프로토타입 참조값에 저장되는 프로토타입은 객체 생성 방식에 의해 결정된다.`
  - `객체 리터럴 방식 {}으로 생성된 객체의 프로토타입은 Object.prototype, 생성자 함수에 의해 생성된 객체의 프로토타입은 생성자 함수의 prototype 프로퍼티에 바인딩되어 있는 객체이다.`
- 모든 객체는 하나의 프로토타입을 갖고, 이는 생성자 함수와 연결되어 있다.
  - 객체 - 프로토타입 - 생성자 함수

#### `__proto__` 접근자 프로퍼티

- 모든 객체는 접근자 프로퍼티를 통해 자신의 프로토타입, [[Prototype]] 내부 슬롯에 간접적으로 접근할 수 있다.
- 내부 슬롯과 내부 메서드는 직접적으로 접근할 수 없으나, 일부 내부 슬롯과 내부 메서드에 간접적으로 접근할 수 있는 수단을 제공한다.
- 접근자 프로퍼티이기 떄문에 `getter/setter` 접근자 함수를 통해 프로토타입을 취득하거나 할당할 수 있다.

  ```jsx
  const example = {};

  console.log(example); // Object {} ( [[Proto]] {...객체의 내부 슬롯과 메서드} )
  console.log(example.__proto__); // Object {} ( {...객체의 내부 슬롯과 메서드} )

  example.__proto__ = { value: "여기는 값" };
  console.log(example); // Object {}
  console.log(example.__proto__); // {value: "여기는 값"}
  ```

- `접근자 프로퍼티는 객체가 직접 소유하고 있는 프로퍼티가 아닌 Object.prototype의 프로퍼티이다.`
- `모든 객체는 상속을 통해 Object.prototype.__proto__ 접근자 프로퍼티를 사용할 수 있다.`

  ```jsx
  const example = {};

  // 직접 소유하지 않음
  console.log(example.hasOwnProperty("__proto__")); // false
  console.log(Object.getOwnPropertyDescriptor(Object.prototype, "__proto__"));

  // {
  //   get: [Function: get __proto__],
  //   set: [Function: set __proto__],
  //   enumerable: false,
  //   configurable: true
  // }

  console.log(example.__proto__ === Object.prototype); // true
  ```

- 접근자 프로퍼티를 통해 프로토타입에 접근하는 이유는, 상호 참조에 의해 프로토타입 체인이 생성되는 것을 방지하기 위해서이다.
- 프로토타입 체인은 단방향 연결 리스트로 구현되어야 하며, 프로퍼티 식별자 검색 방향이 한쪽으로만 흘러야 한다.

  ```jsx
  const parent = {};
  const child = {};

  parent.__proto__ = child;
  child.__proto__ = parent; // TypeError: Cyclic __proto__ value
  ```

- 모든 객체가 접근자 프로퍼티를 사용할 수 있는 것은 아니기 때문에 접근자 프로퍼티를 직접 사용하는 것을 권장하지 않는다.

  - `프로토타입 취득 시 Object.getPrototypeOf 메서드를 사용 권장`
  - `프로토타입 교체 시 Object.setPrototypeOf 메서드를 사용 권장`

  ```jsx
  const parent = { value: 1 };
  const child = {};

  // 프로토타입 취득
  console.log(Object.getPrototypeOf(parent)); // [Object: null prototype] {}

  // 프로토타입 교체, child를 parent로
  Object.setPrototypeOf(child, parent);
  console.log(child); // Object {} [[Prototype]] {value: 1}
  ```

#### 함수 객체의 prototype 프로퍼티

- 생성자 함수로 호출할 수 없는 `non-constructor`인 화살표 함수, 축약 표현으로 정의한 메서드는 prototype 프로퍼티를 소유하지 않고, 프로토타입도 생성하지 않는다.

```jsx
// 함수 객체가 갖는 prototype 프로퍼티
console.log(function () {}.hasOwnProperty("prototype")); // true
// 일반 객체는 prototype 프로퍼티를 갖지 않는다.
console.log({}.hasOwnProperty("prototype")); // false
```

- `모든 객체가 갖고 있는 __proto__ 접근자 프로퍼티와 함수 객체만이 갖고 있는 prototype 프로퍼티는 동일한 프로토타입을 가리킨다.`
- 다만 사용하는 주체가 다르다.
  - `__proto__ 접근자 프로퍼티는 모든 객체가 소유하고 모든 객체가 사용하며, 객체가 자신의 프로토타입 접근 또는 교체하기 위해 사용한다.`
  - `prototype 프로퍼티는 constructor가 소유하고 생성자 함수가 사용하며, 생성자 함수가 자신이 생성할 인스턴스 객체의 프로토타입을 할당하기 위해 사용한다.`

```jsx
function Example(){
  ...
}

const ex = new Example();

// Example 생성자 함수의 prototype 프로퍼티와 ex 인스턴스 객체의 __proto__ 접근자 프로퍼티가 가리키는 것은 동일한 프로퍼티이다.
console.log(ex.__proto__ === Example.prototype); // true
```

##### 프로토타입의 constructor 프로퍼티와 생성자 함수

- 모든 프로토타입은 constructor 프로퍼티를 갖는다.
- 자신을 참조하고 있는 생성자 함수를 가리키며, 이 연결은 생성자 함수가 생성될 때(함수 객체가 생성) 이루어진다.

  ```jsx
  function Example(){
    ...
  }

  const ex = new Example();

  console.log(ex.constructor === Example); // true
  ```
