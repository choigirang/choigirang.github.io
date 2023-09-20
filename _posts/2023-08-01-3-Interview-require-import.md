---
layout: post
title: Interview 21장 - require vs import
author: admin
date: 2023-08-01 00:00:00 +900
lastmod: 2023-08-01 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [INTERVIEW]
tags: [require, import, commonjs, es6]
---

# Interview

## require vs import

- `require, exports`는 NodeJS 에서 사용하고 있는 CommonJS 키워드이다.
- `import, export`는 ES6(ES2015)에서 새롭게 도입된 키워드이다.
- 하나의 프로그램에서 두 키워드를 동시에 사용할 수 없다.
- export 변수와 module.exports 변수를 상황에 맞게 잘 사용해야 한다.
  - 여러 개의 객체를 내보낼 경우 => `export.변수` 의 개별 속성으로 할당
  - 딱 하나의 객체를 내보낼 경우 => `module.exports = 객체` 자체에 할당

```js
// CommonJS
const name = require("./module.js");

const name = "예시";
module.exports = name;
// ES6
import name from "./module.js";

const name = "예시";
export default name;
```

### require()

- CommonJS를 사용하는 NodeJS문이다.
- 파일에 들어있는 곳에 남아있다.
- 프로그램의 어느지점에서나 호출할 수 있다.
- CommonJS 방식으로 모듈을 내보낼 때는 ES6처럼 명시적으로 선언하는 것이 아니라 특정 변수나 그 변수의 속성으로 내보낼 객체를 세팅해줘야 한다.

```js
// 멤버를 직접 일일히 export
exports.name = "Ann"; // 변수의 export
exports.sayHello = function () {
  // 함수의 export
  console.log("Hello World!");
};
exports.Person = class Person {
  // 클래스의 export
  constructor(name) {
    this.name = name;
  }
};

const { name, sayHello, Person } = require("./exportFile.js");

console.log(name); // Ann
sayHello(); // Hello World!
const person = new Person("inpa");
```

### import

- ES6에서 사용된다.
- 항상 맨 위로 이동한다.
- 파일의 시작 부분에서만 실행할 수 있다.
  - 예외적으로 import 전용 비동기 문법으로 파일 중간에 모듈을 불러올 수 있다.
- 사용자가 필요한 모듈 부분만 선택하고 로드할 수 있기 때문에 `require`보다 성능이 우수하며 메모리를 절약할 수 있어 더 선호된다.

```js
// 멤버를 직접 일일히 export
export const name = "Ann"; // 변수의 export
export function sayHello() {
  // 함수의 export
  console.log("Hello World!");
}
export class Person {
  // 클래스의 export
  constructor(name) {
    this.name = name;
  }
}

// ----------------------- OR ----------------------- //

// 멤버를 따로 묶어서 export
const name = "Ann";
function sayHello() {
  console.log("Hello World!");
}
class Person {
  constructor(name) {
    this.name = name;
  }
}
export { name, sayHello, Person }; // 변수, 함수, 클래스를 하나의 객체로 구성하여 export

import { name, sayHello, Person } from "./exportFile.js";

console.log(name); // Ann
sayHello(); // Hello World!
const person = new Person("inpa");

// ----------------------- OR ----------------------- //

// 개별로 export 된걸 * 로 한번에 묶고 as 로 별칭을 줘서, 마치 전체 export default 된걸 import 한 것 처럼 사용 가능
import * as obj from "./exportFile.js";

console.log(obj.name); // Ann
obj.sayHello(); // Hello World!
const person = new obj.Person("inpa");
```

### NodeJs에서 Import

- 정식적으로 노드의 CommonJS를 대체할 순 없지만, 자바스크립트 노드 프로젝트에서 `import`키워드를 사용하도록 지정 설정할 수 있는 방법 또한 존재한다.
- 과거에는 `import, export`를 인식시키기 위해 따로 `.mjs` 확장자를 사용해 모듈 키워드를 구분했지만, 이제는 `package.json`을 통해 확장자를 바꾸지 않아도 `import, export`를 쓸 수 있다.
- `json` 속성에 `"type":"module"`을 추가해준다.

```json
{
  "type": "module"
}
```

```js
// const express = require('express');
import express from "express";

const app = express();

app.get("/", (req, res) => {
  res.send("<h1>Hello World!</h1>");
});

app.listen(3000, () => {
  console.log("3000번 포트 서버 시작");
});
```
