---
layout: post
title: Javascript 26장 - 프로퍼티와 오브젝트
author: admin
date: 2024-07-09 00:00:00 +900
lastmod: 2024-07-09 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [JAVASCRIPT]
tags:
  [
    var,
    let,
    const,
    property,
    object,
    freeze,
    seal,
    prevent,
    extension,
    attribute,
  ]
---

# Javascript

- 처음부터 다시 보는 딥 다이브

## var

- 전역 변수로 사용되며 사용이 지양된다.
- 함수 레벨 스코프로 작동하기 때문에 전역 변수로 남발될 가능성이 높다.
- `var는 선언 단계와 초기화 단계가 함께 이루어지며 런타임에서 할당 단계가 이루어진다.`
  - 호이스팅에 따라 선언과 초기화 단계가 함께 이루어져서 `undefined`가 할당되며, 할당문 전에 접근해도 이미 초기화된 undefined로 접근할 수 있다.

## let

- var의 단점을 보완하기 위해 ES6에서 등장했다.
- 중복 선언은 허용되지 않지만 재할당은 가능하다.
- 블록 레벨 스코프를 따르기 때문에 모든 코드 블록(함수, if, for, while, try, catch 등)에서 지역 스코프로 인정된다.
- 전역 스코프 => 함수 레벨 스코프 => 지역 레벨 스코프

  - 선언 단계 후 TDZ(Temporal Dead Zone, ES6에서 도입된 개념으로, 선언된 시점부터 초기화되기까지의 구간) 구간이 존재, 이후 초기화단계, 다음으로 할당 단계가 이루어진다.
  - 선언만 이루어지고 실제 할당문이 읽힐 때 초기화와 할당 단계가 함께 이루어진다.

  ```jsx
  // 선언만 한 경우
  console.log(example); // TDZ - ReferenceError

  let example; // 선언 및 초기화

  console.log(example); // undefined
  example = "이곳에서 할당이 이루어집니다."; // 할당

  // 선언과 할당이 동시에 이루어진 경우
  console.log(example); // TDZ - ReferenceError

  let example = "선언, 초기화와 할당이 동시에 이루어집니다.";
  ```

## const

- 선언과 초기화를 반드시 동시에 실행해야 하며, 재할당이 금지된다.

  ```jsx
  const example; // SyntaxError

  const example = 1;
  example = 2; // TypeError
  ```

### 느낌표

- 변수의 호이스팅 모두 발생하지만, var, let, const는 다른 규칙을 따른다.
- var는 호이스팅 동시에 초기화 되지만, let과 const는 선언부가 호이스팅되지만 초기화 되지 않고 남아있으며, 할당문을 읽기 전까지의 TDZ 구간을 거친다.

## 내부 슬롯과 내부 메서드

- 자바스크립트의 엔진에서 실제로 작동하지만, 개발자가 직접 접근할 수 있도록 외부로 공개된 객체의 프로퍼티는 아니다.
- 자바스크립트 엔진의 내부 로직이므로 자바스크립트로 직접 접근하거나 호출할 수 있는 방법을 제공하지 않는다.
- `일반 내부 슬롯과 메서드에 대해 접근할 수 있는 수단을 제공하는데, [[Prototype]] 내부 슬롯은 __proto__를 통해 간접 접근이 가능하다.`

### property attribute, descriptors

- 자바스크립트 엔진은 프로퍼티를 생성할 때 프로퍼티의 상태를 나타내는 property attribute 기본값을 자동 정의한다.
- 프로퍼티의 상태
  - `value` : 값
  - `writable` : 갱신 여부 가능, 읽기 전용 여부
  - `enumerable` : 열거 가능 여부, for...in, Object.keys 등의 열거
  - `configurable` : 정의 가능 여부, 프로퍼티의 삭제 및 변경

```jsx
const example = {property1: "value입니다."};

console.log(Object.getOwnPropertyDescriptors(example));

// console
{
  property1 : {value: "value입니다.", writable: true, enumerable: true, configurable: true}
}
```

### data property, getter, setter

- 프로퍼티는 크게 데이터 프로퍼티와 접근자 프로퍼티로 나눌 수 있다.
- 데이터 프로퍼티 : 키와 값으로 구성된 일반적인 프로퍼티
- 접근자 프로퍼티 : 값을 가지고 있지는 않지만 프로퍼티의 값을 읽거나 쓸 때 호출하는 함수로 구성된 프로퍼티

#### 데이터 프로퍼티

- property attribute
  - `[[Value]] value` : 값
  - `[[Writable]] writable` : 갱신 여부 가능, 읽기 전용 여부
  - `[[Enumerable]] enumerable` : 열거 가능 여부, for...in, Object.keys 등의 열거
  - `[[Configurable]] configurable` : 정의 가능 여부, 프로퍼티의 삭제 및 변경

##### 접근자 프로퍼티

- getter/setter 함수라고도 부른다.
- property attribute
  - `[[Get]] get` : 접근자 프로퍼티를 통해 데이터 프로퍼티의 값을 읽을 때 호출되는 접근자 함수
  - `[[Set]] set` : 접근자 프로퍼티를 통해 데이터 프로퍼티의 값을 저장할 때 호출되는 접근자 함수
  - `[[Enumerable]] enumerable` : 데이터 프로퍼티와 동일
  - `[[Configurable]] configurable` : 데이터 프로퍼티와 동일

```jsx
const nameList = {
  person1: "Girang",

  get NameMethod() {
    return console.log(Object.value(this));
  },

  set NameMethod(name) {
    this[`person${Object.keys(name).length}`] = name;
  },
};

nameList.NameMethod; // ["Girang"]
nameList.NameMethod = "Jiyoung";
nameList.NameMethod; // ["Girang", "Jiyoung"]

console.log(Object.getOwnPropertyDescriptor(nameList, "NameMethod"));
// {
//   get: [Function: get NameMethod],
//   set: [Function: set NameMethod],
//   enumerable: true,
//   configurable: true,
// }
```

### 프로퍼티 정의

#### defineProperty, defineProperties

- defineProperty
  - 첫 번째 인자로 추가될 객체를 지정한다.
  - 두 번째 인자로 객체에 추가될 key를 작성한다.
  - 세 번째 인자로 추가될 key의 property attribute를 작성한다.

```jsx
const exObj = {
  property1: "기본값입니다.",
}

Object.defineProperty(exObj, "property2", {
  value: "추가되는 값입니다.",
  writable: true,
  enumerable: true,
  configurable: true
})

console.log(exObj);

{
  property1: "기본값입니다.",
  property2: "추가되는 값입니다."
}
```

- defineProperties
  - 첫 번째 인자로 추가될 객체를 지정한다.
  - 두 번째 인자로 추가될 property attribute를 객체 형태로 직접 작성한다.

```jsx
const exObj = {
  property1: "기본값입니다.",
};

Object.defineProperties(exObj, {
  property2: {
    value: "추가되는 값입니다.",
    writable: true,
    enumerable: true,
    configurable: true,
  },
  property3: {
    value: "두번째 추가되는 값입니다.",
    writable: true,
    enumerable: true,
    configurable: true,
  },
});
```

#### property attribute

- `[[Value]] value`: 생략했을 경우 undefined
- `[[Value]] get`: 생략했을 경우 undefined
- `[[Value]] set`: 생략했을 경우 undefined
- `[[Value]] writable`: 생략했을 경우 false
- `[[Value]] enumerable`: 생략했을 경우 false
- `[[Value]] configurable`: 생략했을 경우 false

### 객체 변경 금지

- 객체는 기본적으로 변경 가능한 값이다.
- 객체의 변경을 금지할 수 있는 메서드가 몇 가지 있는데 각 메서드마다 강도가 다르다.
- 엄격 금지 모드를 사용 시 에러가 발생하며, 아닌 경우 무시된다.

#### preventExtensions

- 프로퍼티 추가 금지
- 프로퍼티 삭제 가능
- 프로퍼티 값 읽기 가능
- 프로퍼티 값 쓰기 가능
- 프로퍼티 어트리뷰트 재정의 가능

```jsx
const canAdd = { default: "value" };
Object.preventExtensions(canAdd);

canAdd["add"] = "added";

console.log(canAdd);
// {
//   default: "value"
// }

delete canAdd.add;
console.log(canAdd);
// { }
```

##### isExtensible

- 확장 가능한 객체인지 판단하는 메서드

#### seal

- 프로퍼티 추가 금지
- 프로퍼티 삭제 금지
- 프로퍼티 값 읽기 가능
- 프로퍼티 값 쓰기 가능
- 프로퍼티 어트리뷰트 재정의 금지

```jsx
const cantDelete = { defalut: "value" };
Object.seal(객체);

delete catDelete.default;
console.log(cantDelete);
// {
//   default: "value"
// }
```

##### isSealed

- 밀봉된 객체인지 판단하는 메서드

#### freeze

- 프로퍼티 추가 금지
- 프로퍼티 삭제 금지
- 프로퍼티 값 읽기 가능
- 프로퍼티 값 쓰기 금지
- 프로퍼티 어트리뷰트 재정의 금지

```jsx
const cantRewrite = {
  default: "value",
};

Object.freeze(cantRewrite);

cantRewrite.default = "Rewrite";

console.log(cantRewrite);
// {
//   default: "value"
// }
```

#### 느낌표

- 앞선 메서드들은 얕은 변경 방지로 직속 프로퍼티만 변경이 금지되기 때문에 중첩 객체에 대해서는 영향을 받지 않는다.
- 객체를 갖는 모든 프로퍼티에 대해서는 재귀 함수를 통해 모든 객체를 금지한다.

```jsx
const nestedObj = {
  property1: "property",
  obj: {
    property1: "property",
  },
};

Object.freeze(nestedObj);
nestedObj.obj.property1 = "Rewrite";
console.log(nestedObj);
// {
//   property1: "property",
//   obj: {
//     property1: "property"
//   }
// }

function freezeDeep(obj) {
  if (obj && typeof obj === "object" && !Object.freeze(obj)) {
    Object.freeze(obj);

    Object.keys(obj).forEach((key) => freezeDeep(obj[key]));
  }
}
```
