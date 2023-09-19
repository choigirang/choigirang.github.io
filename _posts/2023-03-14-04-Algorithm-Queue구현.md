---
layout: post
title: Algorithm 9장 - queue
author: admin
date: 2023-03-14 00:00:00 +900
lastmod: 2023-03-14 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [ALGORITHM]
tags: [algorithm, queue]
---

# Algorithm

## Implementation Queue

- Queue 구현을 위한 기본적인 코드가 작성되어 있습니다. Queue 자료구조의 특성을 이해하고 `FILL_ME_IN` 을 채워 테스트를 통과해주세요.

## 멤버 변수

- 데이터를 저장할 `Object` 타입의 `storage`
- 큐의 가장 앞을 가리키는 `Number` 타입의 포인터 `front`
- '큐의 가장 뒤를 가리키는 `Number` 타입의 포인터 `rear`

## 메서드

- `size()`: 큐에 추가된 데이터의 크기를 리턴해야 합니다.
- `enqueue()`: 큐에 데이터를 추가할 수 있어야 합니다.
- `dequeue()`: 가장 먼저 추가된 데이터를 큐에서 삭제하고 삭제한 데이터를 리턴해야 합니다.

## 주의 사항

- 내장 객체 `Array.prototype`에 정의된 메서드는 사용하면 안 됩니다.
- 포인터 변수 `front`, `rear`의 초기값은 -1, 0, 1등 임의로 지정할 수 있지만 여기서는 0으로 합니다.

## 사용 예시

```js
const queue = new Queue();

queue.size(); // 0
for (let i = 1; i < 10; i++) {
  queue.enqueue(i);
}
queue.dequeue(); // 1
queue.dequeue(); // 2
queue.size(); // 7
queue.enqueue(10);
queue.size(); // 8
queue.dequeue(); // 3
queue.dequeue(); // 4
queue.size(); // 6
```

## 코드

```js
class Queue {
  constructor() {
    this.storage = {};
    this.front = 0;
    this.rear = 0;
  }

  size() {
    return FILL_ME_IN;
  }

  // 큐에 데이터를 추가 할 수 있어야 합니다.
  enqueue(element) {
    this.storage[FILL_ME_IN] = element;
    this.rear += 1;
  }

  // 가장 먼저 추가된 데이터가 가장 먼저 추출되어야 합니다.
  dequeue() {
    // 빈 큐에 dequeue 연산을 적용해도 에러가 발생하지 않아야 합니다
    if (FILL_ME_IN) {
      return;
    }

    const result = this.storage[FILL_ME_IN];
    delete this.storage[FILL_ME_IN];
    this.front += 1;

    return result;
  }
}
```

### 코드 설명

```js
class Queue {
  constructor() {
    this.storage = {};
    this.front = 0;
    this.rear = 0;
  }
  size() {
    return this.rear - this.front;
  }

  // 큐에 데이터를 추가 할 수 있어야 합니다.
  enqueue(element) {
    this.storage[this.rear] = element;
    this.rear += 1;
  }

  // 먼저 추가된 값이 나온다.
  // 가장 먼저 추가된 데이터가 가장 먼저 추출되어야 합니다.
  dequeue() {
    // 빈 큐에 dequeue 연산을 적용해도 에러가 발생하지 않아야 합니다
    if (this.size() === 0) {
      return;
    }

    const result = this.storage[this.front];
    delete this.storage[this.front];
    this.front += 1;

    return result;
  }
}
```

## 코드 설명

- `this.storage = {}`
  - Queue를 구현하기 위한 빈 객체를 선언한다.
- `this.front = 0`
  - Queue는 선입선출이기 때문에 front는 먼저 들어온 값을 나타내기 위한 변수이다.
- `this.rear = 0`
  - Queue에 몇 개의 값이 들어와 있는지 확인하기 위한 값이다.
- `size(){return this.rear - this.front}`
  - Queue에 추가된 데이터의 크기를 리턴해야 한다.
  - Queue가 추가될 때 rear값은 커지고, front가 변경이 된다.
- `enqueue(element){}`
  - `this.storage[this.rear] = element`
    - Queue의 마지막 요소에 인자를 추가한다.
  - `this.rear ++`
    - 인덱스를 증가시킨다.
- `dequeue(){...}`
  - `if(this.size()===0) return;`
    - 빈 큐에 추출하면 그냥 리턴시킨다.
  - `const result = this.storage[this.front]`
    - 추출한 값은 삭제되기 때문에 미리 저장한다.
  - `delete this.storage[this.front]`
    - Queue에서 값을 삭제한다.
  - `this.front += 1`
    - Queue에 몇 개의 값이 존재하는지 알기 위해, size()시 rear에서 front를 빼줄 것이기 때문에 front를 증가시킨다.
