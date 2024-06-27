---
layout: post
title: Interview 26장 - key에 index를 사용하면 안 되는 이유
author: admin
date: 2024-06-27 00:00:00 +900
lastmod: 2024-06-27 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [INTERVIEW]
tags: [key, index]
---

# Interview

## key를 index로 사용하면 안 되는 이유

- 리스트를 매핑하여 여러 개의 동일한 컴포넌트를 사용할 때, 이를 구분짓기 위해 key값이 필요하다.
- `map((value, idx) => ...)`와 같은 형태로 많이 사용되는 키 값은 가상돔이 조정하는 단계에서 각 노드들의 키 값을 비교하여 리스트의 추가 변경 등의 작업을 실행한다.
- 이 때, index가 부여된 컴포넌트는 추가 변경되더라도 매핑되는 인덱스는 변함없이 동일하기 때문에, 컴포넌트의 순서가 변경되어도 동일한 인덱스가 부여되고, 이를 인식하지 못 하여 리렌더링하지 않을 가능성이 있다.
- key값에는 props와 조합하여 고유한 값을 부여하면 다른 컴포넌트의 key값과 중복되는 것을 방지할 수 있다.

```jsx
function MappingList(){
  const data = [...];

  // data의 인덱스를 key값으로 사용할 경우
  // A,B,C 컴포넌트가 순서가 변경되어 C,B,A가 되면
  // key: A(0), B(1), C(2) => C(2), B(1), A(0)
  // 가 되어야 하지만 C(0), B(1), A(2) 로 동일하기 때문에
  // 변화를 인식하지 못 할 수 있다.

  return (
    <ul>
      {/* default */}
      {data.map((value) => <li key={value}>{value}</li>)}
      {/* bad */}
      {data.map((value,idx) => <li key={idx}>{value}</li>)}
      {/* good */}
      {data.map((value,idx) => <li key={`mapping list ${idx}`}>{value}</li>)}
    </ul>
  )
}
```
