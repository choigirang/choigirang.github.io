---
layout: post
title: Algorithm 15장 - tree (2)
author: admin
date: 2023-03-15 00:00:00 +900
lastmod: 2023-03-15 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [ALGORITHM]
tags: [algorithm, tree]
---

# Algorithm

## Implementation Tree

- Tree 구현을 위한 기본적인 코드가 작성되어 있습니다.
- Tree 자료구조의 특성을 이해하고 `FILL_ME_IN` 을 채워 테스트를 통과해주세요.

### 멤버 변수

- 입력 데이터를 담을 수 있는 `value`
- 하위 노드를 저장할 수 있는 `Array` 타입의 `children`

### 메서드

- insertNode(value): 입력받은 value를 Tree에 계층적으로 추가할 수 있어야 합니다.
- contains(value): 트리에 포함된 데이터를 찾을 수 있어야 합니다.

### 주의 사항

- value는 어떠한 값도 들어갈 수 있지만 현재 구현하는 `Tree`는 숫자로 제한합니다.

### 사용 예시

```js
const rootNode = new Tree(null);

for(let i = 0; i <= 4; i++) {
  if(rootNode.children[i]) {
    rootNode.children[i].insertNode(i);
  }
 rootNode.insertNode(i);
}
rootNode; // {value: null, children: Array(5)}
rootNode.contains(5); // false
rootNode.contains(1); // true
...
```

## 코드

```js
class Tree {
  constructor(value) {
    this.value = value;
    this.children = [];
  }

  insertNode(value) {
    const childNode = FILL_ME_IN;
    this.children.push(FILL_ME_IN);
  }

  contains(value) {
    if (FILL_ME_IN) {
      return true;
    }

    return false;
  }
}
```

### 코드 설명

```js
class Tree {
  constructor(value) {
    this.value = value;
    this.children = [];
  }

  insertNode(value) {
    const childNode = new Tree(value);
    this.children.push(childNode);
  }

  contains(value) {
    if (this.value === value) {
      return true;
    }

    for (let i = 0; i < this.children.length; i += 1) {
      const childNode = this.children[i];
      if (childNode.contains(value)) {
        return true;
      }
    }
    return false;
  }
}
```

### 코드 설명

- `constructor(value){...}`
  - `this.value = value`
    - 입력받이 노드의 값이 된다.
  - `this.children = []`
    - 하위 Node들을 저장할 배열을 만든다.
- `insertNode(value){...}`
  - `const childNode = new Tree(value)`
    - 새로운 노드가 하위 노드로 들어갈 것이기 때문에, value를 담은 노드를 변수에 할당한다.
  - `this.chidren.push(childNode)`
    - 만들어진 하위 노드를 기존의 root역할을 할 children에 넣어준다.
- `contains(value){...}`
  - `if(this.value === value) return true`
    - 입력받은 value값이 존재하고 있는 this.value와 같다면 이미 존재하고 있는 노드이기 때문에 true를 리턴한다.
  - `for(...){...}`
    - `const childNode = this.children[i]`
      - 노드가 가진 자식 노드를 확인하는 반복문을 만든다.
      - childNode는 각각의 자식 노드들이 된다.
    - `if(childNode.contains(value))`
      - 만약 자식 노드들 중에 value값을 가진 자식 노드가 있다면 true를 리턴한다.
  - 위 반복문을 통한 조건문이 끝났음에도 true가 없다면 값이 없다는 뜻이기 때문에 false를 리턴한다.
