---
layout: post
title: Algorithm 16장 - graph (2)
author: admin
date: 2023-03-15 00:00:00 +900
lastmod: 2023-03-15 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [ALGORITHM]
tags: [algorithm, graph]
---

# Algorithm

## Implementation Graph

- Graph 구현을 위한 기본적인 코드가 작성되어 있습니다.
- Graph 자료구조의 특성을 이해하고 `FILL_ME_IN` 을 채워 테스트를 통과해 주세요.

### 멤버 변수

- 버텍스와 간선을 담을 `Array` 타입의 matrix

### 메서드

- addVertex(): 그래프에 버텍스를 추가해야 합니다.
- contains(vertex): 그래프에 해당 버텍스가 존재하는지 여부를 Boolean으로 반환해야 합니다.
- addEdge(from, to): fromVertex와 toVertex 사이의 간선을 추가합니다.
- hasEdge(from, to): fromVertex와 toVertex 사이의 간선이 존재하는지 여부를 Boolean으로 반환해야 합니다.
- removeEdge(from, to): fromVertex와 toVertex 사이의 간선을 삭제해야 합니다.

### 주의 사항

- 인접 행렬 방식으로 구현해야 합니다.
- 구현해야 하는 그래프는 방향 그래프입니다.
- 구현해야 하는 그래프는 비가중치 그래프입니다.
- 구현해야 하는 그래프는 이해를 돕기 위해 기존 배열의 인덱스를 정점으로 사용합니다. (0, 1, 2, ... --> 정점)
- 인접 행렬 그래프는 정점이 자주 삭제되는 경우에는 적합하지 않기 때문에 정점을 지우는 메소드는 생략합니다.

### 사용 에시

```js
const adjMatrix = new GraphWithAdjacencyMatrix();
adjMatrix.addVertex();
adjMatrix.addVertex();
adjMatrix.addVertex();
console.log(adjMatrix.matrix);
/*
							TO
		 	  	 0  1  2
		  	0	[0, 0, 0],
	FROM 	1	[0, 0, 0],
		  	2	[0, 0, 0]
*/
let zeroExists = adjMatrix.contains(0);
console.log(zeroExists); // true
let oneExists = adjMatrix.contains(1);
console.log(oneExists); // true
let twoExists = adjMatrix.contains(2);
console.log(twoExists); // true

adjMatrix.addEdge(0, 1);
adjMatrix.addEdge(0, 2);
adjMatrix.addEdge(1, 2);

let zeroToOneEdgeExists = adjMatrix.hasEdge(0, 1);
console.log(zeroToOneEdgeExists); // true
let zeroToTwoEdgeExists = adjMatrix.hasEdge(0, 2);
console.log(zeroToTwoEdgeExists); // true
let oneToZeroEdgeExists = adjMatrix.hasEdge(1, 0);
console.log(oneToZeroEdgeExists); // false

console.log(adjMatrix.matrix);
/*
							TO
		 	  	 0  1  2
		  	0	[0, 1, 1],
	FROM 	1	[0, 0, 1],
		  	2	[0, 0, 0]
*/

adjMatrix.removeEdge(1, 2);
adjMatrix.removeEdge(0, 2);
let oneToTwoEdgeExists = adjMatrix.hasEdge(1, 2);
console.log(oneToTwoEdgeExists); // false
zeroToTwoEdgeExists = adjMatrix.hasEdge(0, 2);
console.log(zeroToTwoEdgeExists); // false

console.log(adjMatrix.matrix);
/*
							TO
		 	  	 0  1  2
		  	0	[0, 1, 0],
	FROM 	1	[0, 0, 0],
		  	2	[0, 0, 0]
*/
```

### 코드

```js
class GraphWithAdjacencyMatrix {
  constructor() {
    this.matrix = [];
  }

  addVertex() {
    const currentLength = this.matrix.length;
    for (let i = 0; i < currentLength; i++) {
      this.matrix[i].push(0);
    }
    this.matrix.push(new Array(currentLength + 1).fill(0));
  }

  contains(vertex) {}

  addEdge(from, to) {
    const currentLength = this.matrix.length;
    if (from === undefined || to === undefined) {
      console.log("2개의 인자가 있어야 합니다.");
      return;
    }

    if (
      "FILL_ME_IN" + 1 > currentLength ||
      "FILL_ME_IN" + 1 > currentLength ||
      "FILL_ME_IN" < 0 ||
      "FILL_ME_IN" < 0
    ) {
      console.log("범위가 매트릭스 밖에 있습니다.");
      return;
    }
  }

  hasEdge(from, to) {}

  removeEdge(from, to) {
    const currentLength = this.matrix.length;
    if (from === undefined || to === undefined) {
      console.log("2개의 인자가 있어야 합니다.");
      return;
    }

    if ("FILL_ME_IN") {
    }
  }
}
```

### 코드 설명

```js
class GraphWithAdjacencyMatrix {
  constructor() {
    this.matrix = [];
  }

  addVertex() {
    const currentLength = this.matrix.length;
    for (let i = 0; i < currentLength; i++) {
      this.matrix[i].push(0);
    }
    this.matrix.push(new Array(currentLength + 1).fill(0));
  }

  contains(vertex) {
    return !!this.matrix[vertex];
  }

  addEdge(from, to) {
    const currentLength = this.matrix.length - 1;
    if (from === undefined || to === undefined) {
      console.log("2개의 인자가 있어야 합니다.");
      return;
    }

    if (from > currentLength || to > currentLength || from < 0 || to < 0) {
      console.log("범위가 매트릭스 밖에 있습니다.");
      return;
    }

    this.matrix[from][to] = 1;
  }
  hasEdge(from, to) {
    return !!this.matrix[from][to];
  }

  removeEdge(from, to) {
    const currentLength = this.matrix.length - 1;

    if (from === undefined || to === undefined) {
      console.log("2개의 인자가 있어야 합니다.");
      return;
    }

    if (from > currentLength || to > currentLength || from < 0 || to < 0) {
      console.log("범위가 매트릭스 밖에 있습니다.");
      return;
    }

    this.matrix[from][to] = 0;
  }
}
```

### 코드 설명

- `constructor(){...}`
  - `this.matrix = []`
    - 그래프의 역할을 할 matix 배열을 만든다.
- `addVertex(){}`
  - `const currentLenth = this.matrix.length`
    - vertex를 추가한다.
    - 현재 matrix에 입력된 갯수를 설정한다.
  - `for(...){...}`
    - `this.matrix[i].push(0)`
    - `this.matrix.push(new Array(currentLength + 1).fill(0))`
      - 갖고 있는 정점들의 관계를 나타내기 위해, 갖고 있는 정점들의 갯수만큼 배열에 0을 추가한다.
      - 만약 정점들이 3개(A,B,C)라면 [0,0,0]을 가진 배열이 3개 존재한다.
- `contains(vertex){...}`
  - `return !!this.matrix[vertex]`
    - 해당 정점이 그래프에 존재하는지 확인하고, 확인 여부에 따라 true,false를 출력한다.
- `addEdge(from, to){...}`
  - `const currentLength = this.matrix.length -1`
    - 임의의 정점부터 정점까지 간선(edge)를 추가한다.
    - matrix에 담겨있는 배열의 갯수를 변수에 할당한다.
  - `if(...){...}`
    - 입력받은 인자가 두 개인지 확인하여 오류 메시지를 출력한다.
  - `if(form > currentLength || to > currentLength || to < 0)`
    - 입력받은 인자가 현재 갖고있는 배열의 갯수보다 넘어갔다는 것에 대한 오류 메시지를 출력한다.
  - `this.matrix[form][to] = 1`
    - 정점의 간선 여부를 나타내는 1값을 입력받은 from순서의 배열에 to번째 요소에 할당한다.
- `hasEdge(from,to){return !!this.matrix[from][to]}`
  - from 해당 정점에서 to 정점으로 가는 간선이 있는지 여부에 따라 true,false를 리턴한다.
- `removeEdge(from,to){...}`
  - `const currentLength = this.matrix.length -1`
    - 현재 들어있는 정점의 갯수를 담는다.
  - `if(from === undefined || to === undefined)`
    - 위와 동일
  - `if(from > currentLength || to > currentLength || from < 0 || to < 0)`
    - 위와 동일
  - `this.matrix[from][to] = 0`
    - 정점의 간선 연결 여부인 0을 할당하여 간선을 삭제한 것을 표현한다.
