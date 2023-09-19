---
layout: post
title: JavaScript 8장 - 개념짚기 (2)
author: admin
date: 2023-06-16 00:00:00 +900
lastmod: 2022-06-20 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [JAVASCRIPT]
tags: [javascript, js, property, method, object]
---

# JavaScript

## Object 객체

- `key`와 `value`로 구성되어 여러 개의 속성을 가질 수 있는 것을 말한다.
- `key`가 갖고 있는 값에 접근하려면 두 가지 방법으로 접근할 수 있다.

```js
const data = {
  sample: "this is a sample",
};

data.sample; // this is a sample
data["sample"]; // this is a sample
```

- 또한 객체에 속성을 추가하거나 삭제할 수도 있다.

```js
const data = {
  sample: "A",
};

data.sample2 = "B";
data["sample3"] = "C";
delete data.sample3;

// data = {sample: "A", sample2: "B"}
```

- `in`연산자를 사용해서 객체 안에 존재하는 속성을 확인할 수 있다.
- 어떤 값이 넘어올지 확신할 수 없을 때 사용한다.

```js
const data = {
  name,
  age
}

"name" in data; // true
"no-data" in data; // false

function user(data){
  if("age" in data || "name" in data){
    return true;
  }else if(!("example" in data)){
    return false;
  }
}

const Mike = {
  age: 30,
  location: seoul
}

const Jane = {
  gender: "girl";
}

user(Mike); // true
user(Jane); // false

for(x in Mike){
  console.log(x)
}
// 30
// seoul
```

## method

- 객체의 속성에 할당 된 함수를 의미한다.

```js
const act = {
  fly: function () {
    console.log("날아요");
  },
  ride: function (data) {
    console.log(data + "를 타고 갑니다.");
  },
};

console.log(act.ride("자전거")); // 자전거를 타고 갑니다.

const example = {
  name: "Jane",
  user: function () {
    console.log(`$(this.name) 안녕하세요.`);
  },
};
```

- `this`는 실행되는 시점에서 결정된다.

```js
hello: function (){
  console.log(`${this.name}, hello`)
}
// 아직 this name이 결정되지 않았다.
let Jane = {
  name : "Jane"
}

let Mike = {
  name: "Mike"
}

Mike.hello(); // Mike, hello
Jane.hello(); // Mike, hello
```

- **화살표 함수는 일반 함수와 달리 자신만의 this를 가질 수 없으며, 화살표 함수에서 this를 사용 시 외부에서 값을 가져온다.**

```js
let boy = {
  name: "Mike",
  hello: () => {
    console.log(`${this.name}, hello`);
  },
};

boy.hello(); // Mike hello가 아닌 전역 객체를 불러온다.
```

- 이점, `this`를 사용하면 참조하는 원본 객체의 값이 변하더라도, 이를 참조하는 다른 객체의 값은 변하지 않는다.

```js
let boy = {
  name: "Mike",
  hello: function () {
    console.log(`${boy.name}, Hello`);
  },
};

let man = boy;
man.name = "Gone";
man.hello(); // Gone, Hello

boy = null;
man.hello(); // Error, 참조하는 boy가 null

let anotherBoy = {
  name: "Gone",
  hello: function () {
    console.log(`${this.name}, hello`);
  },
};

let child = anotherBoy;
anotherBoy = null;
child.hello(); // Gone, hello
```

## 배열

- 객체에 사용하는 `for ... in ...`과 달리 `for ... of ...`와 헷갈리지 않도록 주의해야 한다.

```js
const example = ["1", "2", "3", "4"];

for (const i of example) {
  console.log(i);
}

// 1, 2, 3, 4
```
