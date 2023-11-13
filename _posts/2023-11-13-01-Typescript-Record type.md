---
layout: post
title: TypeScript 13장 - Record type
author: admin
date: 2023-11-13 00:00:00 +900
lastmod: 2023-11-13 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [TYPESCRIPT]
tags: [typescript, javascript, record]
---

# TypeScript

[참고자료1](https://developer-talk.tistory.com/296)<br>
[참고자료2](https://cheeseb.github.io/typescript/typescript-record/)<br>

- 타입스크립트를 사용하다보니 객체에 담겨있는 `key`와 `value`를 가져와서 사용할 일이 생겼다.
- 인덱스 시그니처 타입을 사용하다보니 `Record Type`에 대해 알게 됐고, 더 찾아보기로 하였다.

## 인덱스 시그니처(index signature)

- 인덱스 시그니처 타입은 `[Key:U]:T` 형식의 객체로 여러 `key`를 가질 수 있으며, `key`와 매핑되는 `value`를 가지는 경우 사용한다.
- 객체가 `<Key, Value>` 형식으로 정확하게 `Key`와 `Value`의 타입을 명시해야 되는 경우 사용된다.
- `key` 타입은 `string|number|symbol|Template literal` 타입만 가능하다.
- 매핑이 올바르지 않고 존재하지 않는 속성으로의 접근이 가능하기 때문에 런타임 시점의 타입과 다를 수 있다.

```jsx
// 타입이 없을 시
const example = {
    example1 : "예시입니다.",
    example2 : "예시입니다."
}

// type err
Object.keys(example).map((list, idx) => (
    ...
))
```

```jsx
// 인덱스 시그니처 사용
type ExampleSignature = {
    [key: string] : string
}

const example: ExampleSignature = {
    example1 : "예시입니다.",
    example2 : "예시입니다.",
}

// type ok
Object.keys(example).map((list, idx) => (
    ...
))
```

```jsx
// 존재하지 않는 속성
type UndefinedProperty = {
    [key: string] : string
}

const example: UndefinedProperty = {
    example1: "예시입니다.",
    example2: "예시입니다.",
}

// type ok
Object.keys(example).map((list, idx) => (
    ...
))

console.log(example.example3); // undefined
```

## Record Type

- 타입스크립트 2.1에 도입된 **유틸리티** 타입이다.
- 인덱스 시그니처 타입이 `key`의 의도를 더 명확하게 나타내지만 `Record`를 사용하여 더욱 간단한 작성이 가능하다.

```jsx
type ExampleSignature = {
  [key: string]: string,
};

const example: ExampleSignature = {
  example1: "예시입니다.",
};

const sameExample: Record<string, string> = {
  example1: "예시입니다.",
};
```

### Record 문자열 리터럴

- 인덱스 시그니처 타입과 유사하게 사용이 가능하지만 `key`로 문자열 리터럴을 사용할 수 있는 유틸리티 타입이다.

```jsx
// 문자열 리터럴 에러
// type err
// index signature paramerter ... cannot be literal type
type LiteralSignature = {
    [key: "example1"|"example2"|...]: string;
}

type Example = "example1"|"example2"|...

type RecordExample = Record<Example, strirng>

// type ok
const example: RecordExample = {...}
```

### 2.9버전의 열거형

- 2.9부터는 문자열 리터럴보다 깔끔한 열거형을 `key`로 사용할 수 있다.

```jsx
// 2.9 열거형
type Example = {
  1: "example1",
  2: "example2",
  3: "example3",
};

type RecordExample = Record<Example, string>;

const example: RecordExample = {
  example1: "예시입니다.",
  example2: "예시입니다.",
  example3: "예시입니다.",
};
```

### keyof와 Record

- 타입 또는 인터페이스에 존재하는 모든 키 값을 `union` 형태로 가져온다.

```jsx
type Example = {
    example1: string;
    example2: string;
    example3: string;
}

type RecordExample = Record<keyof Example, string>;

const example: RecordExample = {
    ...
}
```

### 활용

- 인터페이스 타입인 경우 타입 키워드를 사용하는 것과 달리 속성을 따로 지정해서 사용해야 한다.
-

```jsx
interface ExampleKey {
    exampel1: string;
    example2: string;
}

interface ExampleValue {
    exArr1: string;
    exArr2: string;
}

// type err
interface WrongExampleType {
    [K in keyof ExampleKey]: ExampleValue[]
}

// type err
const example:{[K in keyof ExampleKey]: ExampleValue[]} = {...}

// 속성 접근
// type ok
interface TypeExample {
    example: {
        [K in keyof ExampleKey]: ExampleValue[]
    }
}

const example: TypeExample = {
    example: {
        example1 : [exArr1, exArr2],
        example2 : ...,
    }
}

const example: TypeExample["example"] = {
    example1: [exArr1, exArr2],
    example2: ...,
}
```

```jsx
// Record
const example: Record<keyof ExampleKey, ExampleValue[]> = {
    example1: [exArr1, exArr2],
}
```
