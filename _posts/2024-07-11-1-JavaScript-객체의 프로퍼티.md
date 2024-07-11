---
layout: post
title: Javascript 31장 - 객체의 프로퍼티
author: admin
date: 2024-07-11 00:00:00 +900
lastmod: 2024-07-11 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [JAVASCRIPT]
tags: [instanceof, prototype, create, ordinary, key]
---

# Javascript

- 처음부터 다시 보는 딥 다이브

## instanceof

- 우변의 생성자 함수의 프로토타입에 바인딩된 객체가 좌변 객체의 프로토타입 체인 상에 존재하는지를 확인한다.
- 프로토타입의 constructor 프로퍼티가 가리키는 생성자 함수를 찾는 것이 아니다.

```jsx
function Example(){
  ...
}

const ex1 = new Example();
const ex2 = {};

ex1.constructor === Example; // true
```

- 객체 리터럴로 생성한 ex2와 생성자 함수로 생성한 ex1이 있다.

### 상황 1

- 생성자 함수로 생성된 ex1을 객체 리터럴로 생성하였기에 Object를 가리키는 ex2에 프로토타입으로 재설정한다.
- 재설정하였기 때문에 ex1과 Example의 체이닝은 끊기게 된다.

```jsx
function Example(){
  ...
}

const ex1 = new Example();
const ex2 = {};

Object.setPrototypeOf(ex1, ex2)

ex1.constructor === Example; // false
ex1 instaceOf Example; // false
```

### 상황 2

- Object의 프로토타입으로 ex1을 설정한 후 생성자 함수의 프로토타입으로 ex2을 설정 시 ex1과 Example 체이닝은 연결된다.

```jsx
function Example(){
  ...
}

const ex1 = new Example();
const ex2 = {};

Object.setPrototypeOf(ex1, ex2)

Example.prototype = ex2;

ex1.constructor === Example; // false
ex1 instaceOf Example; // true
```

### 상황 3

- 프로토타입에 바인딩했다고 하여 원본으로 하는 생성자 함수를 가리키게 되는 것은 아니다.

```jsx
function Example(){
  ...
}

const ex1 = new Example();
const ex2 = {};

Object.setPrototypeOf(ex1, ex2)

Example.prototype = ex2;

ex2.constructor === Example; // false
```

## 직접 상속

### Object.create

- 프로토타입을 지정하여 새로운 객체를 생성한다.
- 다른 객체 생성 방식과 마찬가지로 OrdinaryObjectCreate를 호출하는 것과 동일하다.
  - 다른 점은 객체를 생성하면서 직접적으로 상속을 구현한다.
  - new 연산자 없이도 객체를 생성할 수 있으며, 프로토타입을 지정하면서 객체를 생성할 수 있다.
  - 객체 리터럴에 의해 생성된 객체도 상속받을 수 있다.
- ESLint 에서는 Object.create의 빌트인 메서드를 객체가 직접 호출하는 것을 권장하지 않는다.
  - Object.create 메서드를 통해 프로토타입 체인의 종점에 위치하는 객체를 생성할 수 있다.
  - 모든 객체의 종점에는 언제나 Object.prototype이 존재한다는 것을 고려했을 때, 직접 프로토타입 체인의 종점에 위치하는 객체를 생성하는 것은 바람직하지 않다.
  - 따라서, Object.prototype의 빌트인 메서드는 간접적으로 호출하는 것이 바람직하다.

```jsx
// obj -> null
let obj = Object.create(null);
console.log(Object.getPrototypeOf(obj) === null); // true

// obj -> Object.prototype -> null
obj = Object.create(Object.prototype);
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true

// obj -> Objrct.prototype -> null
obj = Object.create(Object.prototype, {
  x: { value: 1, writable: true, enumerable: true, configurable: true },
});
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true

const myProto = { x: 10 };
// obj -> myProto -> Object.prototype -> null
obj = Object.create(myProto);
console.log(Object.getPrototypeOf(obj) === myProto); // true

function Person(name) {
  this.name = name;
}
// obj -> Perosn.prototype -> Object.prototype -> null
obj = Object.create(Person.prototype);
console.log(Object.getPrototypeOf(obj) === Person.prototype); // true

// 프로토타입이 null 인 객체를 생성
const obj = Object.create(null);
obj.a = 1;

// console.log(obj.hasOwnProperty("a"));   // TypeError: obj.hasOwnProperty is not a function
console.log(Object.prototype.hasOwnProperty.call(obj, "a")); // true
```

### 객체 리터럴 내부에서 proto에 의한 직접 상속

- Object.create로 객체를 생성할 때, 두 번째 파라미터로 프로퍼티를 정의하는 것에 번거로움을 해소할 수 있다.

```jsx
const myProto = { x: 10 };

// 객체 리터럴에 의해 객체를 생성하면서, 프로토타입을 지정하여 직접 상속을 구현할 수 있다.
const obj = {
  y: 20,
  // obj -> myProto -> Object.prototype -> null
  __proto__: myProto,
};
/*
위 obj 정의는 다음과 같다.
const obj = Object.create(myProto, {
  y: { value: 20, writable: true, enumerable: true, configurable: true }
})
*/

console.log(obj.x, obj.y); // 10 20
console.log(Object.getPrototypeOf(obj) === myProto); // true
```

## 정적 프로퍼티와 메서드

- 생성자 함수로 인스턴스를 생성하지 않아도 참조/호출 가능한 프로퍼티/메서드
- 생성자 함수도 객체이다.
- 그러므로, 생성자 함수도 프로퍼티나 메서드를 소유할 수 있다.
- 생성자 함수가 소유한 프로퍼티나 메서드를 정적 프로퍼티/메서드라고 한다.
- 생성자 함수가 소유하고 있는 정적 프로퍼티나 메서드는 인스턴스에서 직접 참조/호출할 수 없다.

```jsx
function Person(name){
  this.name = name
}

// 프로토타입 메서드
Person.prototype.say = function(){
  ...
}

// 정적 프로퍼티
Person.staticProp = "";

// 정적 메서드
Person.staticMethod = function(){
  ...
}

const me = new Person("");

Person.staticMethod(); // ""
me.staticMethod(); // TypeError
```

## 프로퍼티 존재 확인

- `in`
  - 확인 대상 객체가 상속받은 모든 프로토타입의 프로퍼티 또한 확인하기 때문에 주의하여야 한다.
- `hasOwnProperty`

```jsx
"example" in obj;
obj.prototype.hasOwnProperty("example");
```

## 프로퍼티 열거

- `fon...in`
  - 객체의 모든 프로퍼티를 순회하며 열거할 때 사용
  - `객체의 프로토토타입 체인 상에서 존재하는 모든 프로퍼티 중에서 프로퍼티 어트리뷰트 [[Enumerable]] true인 값을 순회하며 열거한다.`
  - 프로퍼티 키가 Symbol인 프로퍼티는 열거하지 않는다.
  - for - in 문은 순서를 보장하지 않는다.
  - `해당 객체의 프로퍼티 키들로만 for - in 문을 순회하고 싶을 때는 Object.prototype.hasOwnProperty 메서드를 호출하며 검사한다.`

```jsx
const person = {
  name: "WI",
  age: 100,

  __proto__: {
    address: "Incheon",
  },
};

console.log("toString" in person); // true
for (const key in person) {
  console.log(`${key} : ${person[key]}`);
}
/*
name : WI
age : 100
address : Incheon
*/

for (const key in person) {
  // person 객체의 고유 프로퍼티일 경우에만 정보를 출력
  if (person.hasOwnProperty(key)) {
    console.log(`${key} : ${person[key]}`);
  }
}
/*
name : WI
age : 100
*/
```
