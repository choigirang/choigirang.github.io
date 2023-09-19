---
layout: post
title: Algorithm 18장 - postorder
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

## postorder

- 이진트리의 root노드를 줄 것입니다. 해당 이진트리의 후위 순회 결과를 출력하세요.

### 입력

- 인자 1 : TreeNode
  - `TreeNode` 타입으로 된 root 노드

### 출력

- `number[]` 을 리턴해야 합니다.

### 주의 사항

- 이진트리 내의 노드 갯수의 범위는 0 - 100 입니다.
- 해당 문제를 재귀적인 해결책과 반복적인 해결책 모두를 사용해 풀어보세요.

### 입출력 예시

```js
class TreeNode {
  constructor(val, left, right) {
    this.val = val === undefined ? 0 : val;
    this.left = left === undefined ? null : left;
    this.right = right === undefined ? null : right;
  }
}

//     1
//      \
//       2
//      /
//     3
const root1 = new TreeNode(1, null, new TreeNode(2, new TreeNode(3), null));
const result1 = postOrderTraversal(root1);
console.log(result1); // [3, 2, 1]

const root2 = null;
const result2 = postOrderTraversal(root2);
console.log(result2); // []

const root3 = new TreeNode(1);
const result3 = postOrderTraversal(root3);
console.log(result3); // [1]
```

### 코드

```js
/// class TreeNode{
//   constructor(val = 0, left = null, right = null){
//     this.val = val;
//     this.left = left;
//     this.right = right;
//   }
// }
const postOrderTraversal = (root) => {};
```

### 코드 설명

```js
const postOrderTraversal = (root) => {
  if (!root) return [];
  return [
    ...postOrderTraversal(root.left),
    ...postOrderTraversal(root.right),
    root.val,
  ];
};
```

### 코드 설명

- 후위순회를 하여 나온 값을 배열에 차례대로 넣어 출력하는 문제이다.
- 입출력 에시를 보면 입력받은 val와 left, right는 차례대로 루트,왼쪽 노드, 오른쪽 노드가 된다.
- root는 트리를 만드는 코드를 갖고 있으며 root.left, root.right, root.val 를 차례대로 재귀를 돌려 나온 결괏값을 배열에 펼쳐 담는다.
