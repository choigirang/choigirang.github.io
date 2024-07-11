---
layout: post
title: Javascript 33장 - 함수의 구분과 화살표 함수의 차이점
author: admin
date: 2024-07-11 00:00:00 +900
lastmod: 2024-07-11 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [JAVASCRIPT]
tags: [arrow, function, es6, rest, constructor, super, binding]
---

# Javascript

- 처음부터 다시 보는 딥 다이브

## 함수의 구분

- ES6 이전에는, 모든 함수는 일반 함수로 호출할 수 있는 것은 물론 생성자 합수로도 호출할 수 있는 `callable & constructor`였다.
- ES6 이후에는 함수의 사용 목적에 따라 명확히 구분된다.
- `일반 함수 : constructor & prototype & arguments`
- `메서드 : super & arguments`
- `화살표 함수 : 전부 불가능`

### 메서드

- ES6 이후, 메서드는 메서드 축약 표현으로 정의된 함수만을 의미한다.
  - 메서드는 인스턴스를 생성할 수 없는 non-constructor 이기 때문에 생성자 함수로 호출할 수 없다.
  - prototype 프로퍼티가 없으며, 프로토타입을 생성하지 않는다.
- ES6 메서드는 자신을 바인딩한 객체를 가리키는 [[HomeObject]] 내부 슬롯을 갖는다.
  - super 참조는 [[HomeObject]] 를 참조하여 슈퍼클래스의 메서드를 참조하기 때문에 super 키워드를 사용할 수 있다.

```jsx
const obj = {
  value: 1,
  // 축약 O => 메서드 O
  method() {
    console.log(this.value);
  },
  // 축약 X => 메서드 X
  // non-constructor, prototype X
  notMetnod: function () {
    console.log(this.value);
  },
};
```

### 화살표 함수

- 콜백 함수 내부에서 this가 전역 객체를 가리키는 문제를 해결하기 위한 대안으로 사용된다.
- 화살표 함수는 즉시 실행 함수로 사용이 가능하며, 일급 객체이기 때문에 고차함수에 인수로 전달이 가능하다.

  - 콜백 함수를 정의할 때 유용하다.

  ```jsx
  const ex = ((name) => ({
    say(){console.log(name)};
  }))("Rang")

  ex.say();

  [1,2,3,].map((x) => x ** x); // [....]
  ```

- 화살표 함수는 인스턴스를 생성할 수 없는 non-constructor 다.

  ```jsx
  const Cant = () => {};
  new Cant(); // TypeError
  ```

- 중복된 매개변수 이름을 선언할 수 없다. (단, strict mode에서)

  ```jsx
  "use strict";
  function cant(a, a) {
    return a + a;
  }
  // SyntaxError
  ```

- 화살표 함수는 함수 자체의 this, arguments, super, new.target 바인딩을 갖지 않는다.
- 따라서 스코프 체인을 통해 상위 스코프의 this, arguments, super, new.target을 참조해야 한다.
- 화살표 함수가 중첩되어 있어도, 화살표 함수들은 자체적으로 바인딩하지 않기 때문에 외부에 가장 가까운 상위 스코프 함수에 바인딩한다.

  ```jsx
  class Binding {
    constructor(price) {
      this.price = price;
    }

    sell(arr) {
      return arr.map(function (income) {
        return this.price + income;
      });
    }

    // 외부 참조 this = constructor price
    canSell(arr) {
      return arr.map((income) => this.price + income);
    }
  }

  const product = new Binding(100);
  product.sell([100, 200]); // TypeError
  product.canSell([100, 200]); // [200, 300]
  // class에서는 암묵적으로 strict mode가 적용되고
  // 일반 함수로 전달한 콜백 함수 내에서 this는 undefined를 바인딩하기 때문에
  // 화살표 함수로 외부를 참조할 수 있다.
  ```

#### 화살표 함수의 this

- 화살표 함수는 다른 함수의 인수로 전달되어 콜백 함수로 사용하는 경우가 많다.
- `화살표 함수의 this는 일반 함수와 다르게 작동한다.`
- `일반 함수 내부의 this는 함수가 누구에 의해 호출되었는지에 따라 동적으로 결정한다.`
- 고차 함수의 인수로 전달한 콜백 함수가 일반 함수일 경우 내부에서 this를 참조 시 undefined이다.
  - 일반 함수로 정의된 콜백 함수의 경우, 기본적으로 전역 객체에 바인딩 되며 strict mode에서는 undefined가 바인딩 된다.
  - `일반 함수로 콜백 함수를 전달할 경우 발생하는 콜백 함수 내부의 this 문제이다.`
  - `콜백 함수의 this와 외부 함수의 this가 서로 다른 값을 가리키기 때문에 TypeError가 발생한다.`
  - ES6 이전에는 이를 해결하기 위해, 참조할 this를 콜백 함수 외부에서 변수로 저장한 뒤 콜백 함수 내부에서 사용한다.
  - 고차 함수의 두 번째 인수로 사용할 this를 전달하고 Function.prototype.bind 메서드로 사용할 this를 바인딩한다.
- `콜백 함수 내부의 this 문제를 해결하기 위해 설계된 것으로 함수 자체의 this 바인딩을 갖지 않는다.`
  - `함수 내부에서 this를 참조하면 상위 스코프의 this를 참조한다.`
  - `마치 렉시컬 스코프와 같이 함수가 정의된 위치에 따라 결정되는 것처럼 보이며, 함수 자체의 this 바인딩을 갖지 않기 때문에 Function.prototype에 apply, call, bind 메서드를 사용해도 화살표 함수 내부의 this를 교체할 수 없다.`

#### 화살표 함수의 super

- `화살표 함수는 함수 자체의 super 바인딩도 갖지 않는다.`
- `함수 내부에서 super를 참조하면 this와 마찬가지로 상위 스코프의 super를 참조한다.`

```jsx
class Base {
  constructor(name) {
    this.name = name;
  }

  say() {
    return `Name is ${this.name}`;
  }
}

class Arrow extends Base {
  // 화살표 함수의 super는 상위 스코프인 constructor의 super
  // Arrow의 say 함수는 화살표 함수이기 때문에 super 바인딩을 갖지 않고,
  // constructor의 super를 참조한다.

  say = () => `Name is ${super.say()}`;
}

const printName = new Arrow("Arrow Func"); // Name is Arrow Func
```

#### 화살표 함수의 arguments

- `화살표 함수는 함수 자체의 arguments 바인딩도 갖지 않는다.`
- `화살표 함수 내부에서 arguments 객체를 참조하면 this와 마찬가지로 상위 스코프의 arguments를 참조한다.`
- `화살표 함수는 arguments 객체를 사용할 수 없기 때문에, 자신에게 전달된 인수 목록을 확인할 수 없고, 상위 함수의 인수 목록만 참조하므로 쓸모가 없다.`
- `화살표 함수로 가변 인자 함수를 구현해야 할 때는 반드시 Rest 파라미터를 사용해야 한다.`

```jsx
const arrow = () => console.log(arguments);

arrow(1, 2); // RefrerenceError
```

### Rest 파라미터

- 함수의 매개변수 이름 앞에 세 개의 점(...)을 붙여서 정의한 매개변수를 의미한다.
- Rest 파라미터는 함수에 전달된 인수들의 목록을 배열로 전달 받는다.
- 일반 매개변수와 Rest 파라미터는 혼용해서 사용이 가능하다.
  - 혼용해서 사용할 경우, Rest 파라미터는 항상 매개변수 마지막에 위치해야 한다.
- Rest 파라미터는 하나만 사용해야 한다.
- Rest 파라미터는 선언한 매개변수 개수를 나타내는 length 프로퍼티에 영향을 주지 않는다.

```jsx
function normal(a, b, ...rest) {
  console.log(a, b, rest, normal.length);
}

normal(1, 2, 3, 4, 5); // 1, 2, [3,4,5] 2
```

### 매개변수 기본값

- 자바스크립트 함수에서 인수가 전달되지 않은 배개변수는 undefined 이다.
- 자바스크립트 엔진에서 매개변수의 개수와 인수의 개수를 체크하지 않기 때문이다.
- 일종의 방어 코드로 매개변수의 기본값을 전달할 필요가 있다.
- ES6 도입된 매개변수 기본값을 사용하면 함수 내에서 수행하던 인수 체크 및 초기화를 간소화 할 수 있다.
- Rest 파라미터에는 기본값을 지정할 수 있다.
- 매개변수 기본값은 length 프로퍼티와 arguments 객체에 영향을 주지 않는다.

```jsx
function sum(a = 0, b = 0) {
  return a + b;
}

sum(); // 0
sum(1); // 1
sum(1, 2); // 3
```
