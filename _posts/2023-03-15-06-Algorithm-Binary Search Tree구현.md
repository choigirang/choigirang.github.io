---
layout: post
title: Algorithm 17장 - Binary Search Tree
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

## Implementation Binary Search Tree

- Tree 구현을 위한 기본적인 코드가 작성되어 있습니다.
- Binary Search Tree 자료구조의 특성을 이해하고 `FILL_ME_IN` 을 채워 테스트를 통과해주세요.

### 멤버 변수

- 입력 데이터를 담을 수 있는 value
- 노드를 왼쪽에 저장할 수 있는 `Array` 타입의 left
- 노드를 오른쪽에 저장할 수 있는 `Array` 타입의 right

### 메서드

- insert(value): 입력받은 value를 Binary Search에 맞게 Tree에 계층적으로 추가할 수 있어야 합니다.
- contains(value): 트리에 포함된 데이터를 찾을 수 있어야 합니다.
- preorder(callback): 전위 순회를 통해 트리의 모든 요소에 callback을 적용할 수 있어야 합니다.
- inorder(callback): 중위 순회를 통해 트리의 모든 요소에 callback을 적용할 수 있어야 합니다.
- postorder(callback): 후위 순회를 통해 트리의 모든 요소에 callback을 적용할 수 있어야 합니다.

### 주의 사항

- value는 어떠한 값도 들어갈 수 있지만 현재 구현하는 Tree는 숫자로 제한합니다.

### 사용 예시

```js
const rootNode = new BinarySearchTree(10);
rootNode.insert(7);
rootNode.insert(8);
rootNode.insert(12);
rootNode.insert(11);
rootNode.left.right.value; // 8
rootNode.right.left.value; //11

let arr = [];
rootNode.preorder((value) => arr.push(value * 2));
arr; // [20, 14, 16, 24, 22]
...
```

### 코드 설명

```js
class BinarySearchTree {
  constructor(value) {
    this.value = value;
    this.left = null;
    this.right = null;
  }

  insert(value) {
    if (value < this.value) {
      if (this.left === null) {
        this.left = new BinarySearchTree(value);
      } else {
        this.left.insert(value);
      }
    } else if (value > this.value) {
      if (this.right === null) {
        this.right = new BinarySearchTree(value);
      } else {
        this.right.insert(value);
      }
    } else {
    }
  }

  contains(value) {
    if (value === this.value) {
      return true;
    }
    if (value < this.value) {
      return !!(this.left && this.left.contains(value));
    }
    if (value > this.value) {
      return !!(this.right && this.right.contains(value));
    }
  }

  preorder(callback) {
    callback(this.value);
    if (this.left) {
      this.left.preorder(callback);
    }
    if (this.right) {
      this.right.preorder(callback);
    }
  }

  inorder(callback) {
    if (this.left) {
      this.left.inorder(callback);
    }
    callback(this.value);
    if (this.right) {
      this.right.inorder(callback);
    }
  }

  postorder(callback) {
    if (this.left) {
      this.left.postorder(callback);
    }
    if (this.right) {
      this.right.postorder(callback);
    }
    callback(this.value);
  }
}
```

### 코드 설명

- `constructor(value){...}`
  - `this.value = value`
  - `this.left = null`
  - `this.rigth = null`
    - 이진트리를 만든다.
- `insert(value){...}`
  - `if(value < this.value){...}`
    - `if(this.left === null){...}`
      - `this.left = new BinarySearchTree(value)`
        - 왼쪽 노드에 값이 있는지를 확인하여 값이 없는 경우에는
        - 만약 입력받은 값이 루트인 value보다 클 작은 경우 왼쪽 노드로 진행되기 때문에, left에 새로운 노드를 만든다.
    - `else{this.left.insert(value)}`
      - 값이 있는 경우에는 자식 노드에서 재귀를 사용하여, 값이 있는 노드를 루트로 기준하여 자식 노드를 만든다.
  - `else{value > this.value}{...}`
    - `if(this.right === null){...}`
      - `this.right === new BinarySearchTree(value)`
        - 오른쪽 노드에 값이 있는지를 확인하여 값이 없는 경우에는
        - 만약 루트의 값보다 입력받은 값이 클 경우 오른쪽 노드로 진행되기 떄문에, right에 새로운 노드를 만든다.
      - `else{this.right.insert(value)}`
        - 값이 있는 경우에는 자식 노드에서 재귀를 사용하여, 값이 있는 노드를 루트로 기준하여 자식 노드를 만든다.
- `contains(value){...}`
  - `if(value === this.value){return true}`
    - 루트를 기준으로 입력받은 값이 루트와 일치한다면 true를 출력한다.
  - `if(value < this.value){...}`
    - `return !!(this.left && this.left.contains(value))`
      - 만약 입력받은 값이 루트의 값보다 작다면 왼쪽 노드로 진행되야 하기 때문에 왼쪽 노드에서 재귀를 사용하여, 왼쪽의 노드를 루트로 기준하여 값을 찾는다.
  - `if(value > this.value){...}`
    - `return !!(this.right && this.right.contains(value))`
      - 만약 입력받은 값이 루트의 값보다 크다면 오른쪽 노드로 진행되야 하기 때문에 오른쪽 노드에서 재귀를 사용하여, 오른쪽의 노드를 루트로 기준하여 값을 찾는다.
- `preorder(callback){...}`
  - `callback(this.value){...}`
  - `if(this.left){this.left.preorder(callback)}`
  - `if(this.right){this.right.preorder(callback)}`
    - 전위 순회하여 모든 노드 요소에 callback을 적용하는데, 전위 순회의 순서는 루트 > 왼쪽 노드 > 오른쪽 노드로 진행된다.
- `inorder(callback){...}`
  - `if(this.left){this.left.preorder(callback)}`
  - `callback(this.value){...}`
  - `if(this.right){this.right.preorder(callback)}`
    - 중위 순회하여 모든 노드 요소에 callback을 적용하는데, 중위 순회의 순서는 왼쪽 노드 > 루트 > 오른쪽 노드로 진행된다.
- `postorder(callback){...}`
  - `if(this.left){this.left.preorder(callback)}`
  - `if(this.right){this.right.preorder(callback)}`
  - `callback(this.value){...}`
    - 후위 순회하여 모든 노드 요소에 callback을 적용하는데, 후위 순회의 순서는 왼쪽 노드 > 오른쪽 노드 > 루트로 진행된다.
