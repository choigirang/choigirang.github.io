---
layout: post
title: React 50장 - React.Fragment
author: admin
date: 2023-07-31 00:00:00 +900
lastmod: 2023-07-31 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, fragment]
---

# React

- 리액에서 컴포넌트가 리턴하는 값은, 엘리먼트들은 반드시 단 하나의 최상위 태그로 묶여있어야 한다는 법칙을 따른다.
- 그렇기에 여러 요소들을 감싸주기 위한 의미없는 태그를 쓸 때가 생기며, 이는 CSS를 관리하기에도 `table`같이 정해진 규칙을 따라야 할 때도 여러 에러가 발생할 수 있다.
- 이때 사용하는 것이 `React.Fragment`이며 줄여서 `<></>`로 간단하게 쓰이기도 한다.
- `map`함수를 사용하여 배열을 순회한 결과를 만들 때, `key`값을 작성할 때는 `React.Fragment`를 직접적으로 명시해주어야 한다.

```js
const todoList = ["예시", "만들", "배열"];

return todoList.map((todo) => (
  <>
    <td>{todo}</td>
  </>
));
// 에러 key
// div 사용 시 table 에러

return todoList.map((todo, idx) => (
  <React.Fragment key={idx}>
    <td>{todo}</td>
  </React.Fragment>
));
```
