---
layout: post
title: React 18장 - 상태관리
author: admin
date: 2023-02-23 00:00:00 +900
lastmod: 2023-02-23 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]  # 대문자로 작성
tags: [react, components, jsx, state, ui, props]
---
# React
## 상태
- 프론트엔드 개발에서 UI에 **동적으로 표현될 데이터**를 말한다.
- 예를 들어, 쇼핑몰의 장바구니 안에는 상품의 선택 여부, 선택한 상품의 수량, 장바구니에 담긴 물품, 선택한 물품에 따른 주문 금액 등이 변할 수 있는 상태이다.
- 상태에 따라 어떤 화면이 영향을 받고, 이 화면을 컴포넌트로 분리하여 컴포넌트가 서로 어떠한 상태를 공유하고 주고받는다.


### 예시
- 상태는 크게 두 가지로 나눌 수 있는데, 특정 컴포넌트 안에서만 관리되는 로컬상태, 전체 혹은 여러 가지 컴포넌트가 동시에 관리하는 전역 상태가 있다.
  - 장바구니 예시
  - 로컬 상태의 예시는 선택한 수량이 있다.
    - 원래 가격에 상태를 곱해 컴포넌트 내에 표시만 해주면 되고, 다른 컴포넌트와 데이터를 공유하지 않는 `form`을 가지는 `input box`,`select box`를 에로 들 수 있다.
  - 전역 상태의 에시는 총 주문 금액이 있다.
    - 상품 선택 여부에 따라 총 주문을 업데이트해야 하기에 장바구니에 담긴 물품이 그 갯수를 다른 컴포넌트에 전달해 주어야 한다.
    - 또한 장바구니 데이터를 서버에서 받아와야 한다면 `API`를 호출해야 하고, 데이터의 전송 여부에 따라 로딩 여부를 나타낼 것인지도 고려해야 한다.
    - `API`를 사용하는 것도 `side effect`를 발생시키며 로딩 여부 상태도 전역 상태로 볼 수 있다.
- 서로 다른 컴포넌트가 사용하는 상태의 종류가 다르다면 꼭 전역 상태일 필요는 없지만, 서로 다른 컴포넌트가 동일한 상태를 다룬다면 출처는 오직 한 곳이어야 한다.
  - 만일 사본이 있을 경우, 두 데이터는 동기화 과정이 필요한데, 이는 문제를 복잡하게 만들기 떄문에 한 곳에서만 상태를 저장하고 접근해야한다.
- 데이터의 무결성을 위해 동일한 데이터는 항상 같은 곳에서 데이터를 가지고 와야하며, `Single source of truth(신뢰할 수 있는 단일 출처)` 원칙을 지켜야 한다.
  - 데이터가 존재하고 데이터를 보여줘야 하는 우리의 의도와 다른 예외 상황이 없도록 해야 한다.
- 다크 모드의 경우, 모든 페이지, 모든 컴포넌트에 다크 모드 혹은 라이트 모드가 적용 되어야 하기 때문에 테마 설정을 전역으로 관리하는 것을 예로 들 수 있다.


### 상태 관리를 위한 툴
- `React Context`
- `Redux`
- `MobX`
- 상태 관리를 위한 툴은, `props drilling(프로퍼티 내려꽂기)`문제를 해결해준다.
  - A 라는 컴포넌트에 상태가 있고, 마지막 컴포넌트인 I 라는 컴포넌트가 해당 상태를 사용한다면, 중간에 위치한 C, G 컴포넌트는 A의 `name`이라는 상태가 필요하지 않음에도 컴포넌트에 `props`를 만들어 자식 컴포넌트에 넘겨주어야 하는 문제점이다.
- 상태 관리 툴은 필요에 따라 사용되며, 꼭 필요한 툴은 아니다.


## Props Drilling
- 상위 컴포넌트에서 하위 컴포넌트로 `state`를 전달하고자 할 때, 상태값을 `props`를 통해 전달하는 용도로만 쓰이는 형상을 말한다.
- 전달 횟수가 6회 이내로 많지 않다면 큰 문제가 되지 않지만, 규모가 커지고 복잡해지며 문제가 발생한다.
  - 코드의 가독성이 매우 나빠진다.
  - 코드의 유지보수 또한 힘들어진다.
  - `state`변경 시 `props`전달 과정에서 불필요하게 관여된 컴포넌트들 또한 re-render되야 하기 때문에 웹 성능에 악영향을 준다.

### 해결 방법
- 과도한 `props drilling`을 방지하기 위해 컴포넌트와 관련있는 `state`는 될 수 있으면 가까이 유지하는 방법과 상태관리 라이브러리를 사용하는 방법이 있다.
- 상태관리 라이브러리를 사용하게 되면 전역으로 관리하는 저장소에서 직접 `state`를 꺼내쓸 수 있기 때문에 `props drilling`을 방지하기에 매우 효과적이다.

### 예시
- `props drilling`
- 상위 컴포넌트에서 하위 컴포넌트로 `props` 끝까지 전달해준다.
- [StackBlitz로 보기](https://stackblitz.com/edit/react-bvmiba?file=src%2FApp.js){: target="_blank"}



```jsx
import React, { useState } from 'react';
import styled from 'styled-components';

const Container = styled.div`
  border: 5px solid green;
  padding: 10px;
  margin: 10px;
  position: relative;
`;

const Quantity = styled.div`
  text-align: center;
  color: red;
  border: 5px solid red;
  padding: 3px;
  font-size: 1.2rem;
`;

const Button = styled.button`
  margin-right: 5px;
`;

const Text = styled.div`
  color: ${(props) => (props.color ? props.color : 'black')};
  font-size: ${(props) => (props.size ? props.size : '1rem')};
  font-weight: ${(props) => (props.weight ? '700' : 'inherit')};
`;

export default function App() {
  const [number, setNumber] = useState(1);

  const plusNum = () => {
    setNumber(number + 1);
  };

  const minusNum = () => {
    setNumber(number - 1);
  };
  console.log('Parents');
  return (
    <Container>
      <Text weight size="1.5rem">
        [Parents Component]
      </Text>
      <Text>
        Child4 컴포넌트에 있는 버튼을 통해
        <br /> state를 변경하려고 합니다.. 🤮
      </Text>
      <Text weight color="tomato">
        Props Driling이 발생!!
      </Text>
      <Quantity>{`수량 : ${number}`}</Quantity>
      <Child1 plusNum={plusNum} minusNum={minusNum} />
    </Container>
  );
}

function Child1(
  {
    /* props로 전달받은 plusNum, minusNum를 가져오세요 */
  }
) {
  console.log('Child1');
  return (
    <Container>
      <Text>[Child 1 Component]</Text>
      {/* plusNum, minusNum 함수를 props로 전달해주세요! */}
      <Child2 />
    </Container>
  );
}

function Child2(
  {
    /* props로 전달받은 plusNum, minusNum를 가져오세요 */
  }
) {
  console.log('Child2');
  return (
    <Container>
      <Text>[Child 2 Component]</Text>
      {/* plusNum, minusNum 함수를 props로 전달해주세요! */}
      <Child3 />
    </Container>
  );
}

function Child3(
  {
    /* props로 전달받은 plusNum, minusNum를 가져오세요 */
  }
) {
  console.log('Child3');
  return (
    <Container>
      <Text>[Child 3 Component]</Text>
      {/* plusNum, minusNum 함수를 props로 전달해주세요! */}
      <Child4 />
    </Container>
  );
}

function Child4({ plusNum, minusNum }) {
  console.log('Child4');
  return (
    <Container>
      <Text>[Child 4 Component]</Text>
      <Button onClick={plusNum}>👍</Button>
      <Button onClick={minusNum}>👎</Button>
    </Container>
  );
}
```


- 라이브러리 사용 예시
- [StackBlitz로 보기](https://stackblitz.com/edit/react-dky3fz?file=src%2FApp.js){: target="_blank"}



```jsx
import React, { useState } from 'react';
import styled from 'styled-components';
import { useSelector, useDispatch } from 'react-redux';

const Container = styled.div`
  border: 5px solid green;
  padding: 10px;
  margin: 10px;
  position: relative;
`;

const Quantity = styled.div`
  text-align: center;
  color: red;
  border: 5px solid red;
  padding: 3px;
  font-size: 1.2rem;
`;

const Button = styled.button`
  margin-right: 5px;
`;

const Text = styled.div`
  color: ${(props) => (props.color ? props.color : 'black')};
  font-size: ${(props) => (props.size ? props.size : '1rem')};
  font-weight: ${(props) => (props.weight ? '700' : 'inherit')};
`;

export default function App() {
  const number = useSelector((state) => state);
  console.log('Parents');
  return (
    <Container>
      <Text weight size="1.5rem">
        [Parents Component]
      </Text>
      <Text>
        Child4 컴포넌트에 있는 버튼을 통해 <br /> state를 변경하려고 합니다. ☺️
      </Text>
      <Text weight color="tomato">
        (Redux를 사용하는 경우)
      </Text>
      <Quantity>{`수량 : ${number}`}</Quantity>
      <Child1 />
    </Container>
  );
}

function Child1() {
  console.log('Child1');
  return (
    <Container>
      <Text>[Child 1 Component]</Text>
      <Child2 />
    </Container>
  );
}

function Child2() {
  console.log('Child2');
  return (
    <Container>
      <Text>[Child 2 Component]</Text>
      <Child3 />
    </Container>
  );
}

function Child3() {
  console.log('Child3');
  return (
    <Container>
      <Text>[Child 3 Component]</Text>
      <Child4 />
    </Container>
  );
}

function Child4() {
  const dispatch = useDispatch();
  const plusNum = () => {
    dispatch({ type: 'Plus' });
  };

  const minusNum = () => {
    dispatch({ type: 'Minus' });
  };
  console.log('Child4');
  return (
    <Container>
      <Text>[Child 4 Component]</Text>
      <Button onClick={plusNum}>👍</Button>
      <Button onClick={minusNum}>👎</Button>
    </Container>
  );
}
```


### 예시 2
- 느낌표가 하나씩 추가되는 애플리케이션
- Child3, Child6는 하나의 상태를 공유하기 때문에 최상위 컴포넌트인 App에서 사애를 관리한다.
- 따라서, 상태가 변경될 때마다 App 컴포넌트가 re-render되며 모든 컴포넌트가 re-render된다.
- [StackBlitz로 보기](https://stackblitz.com/edit/react-ts-qpgq6d?file=App.js){: target="_blank"}



```jsx
import * as React from 'react';
import './style.css';
import styled from 'styled-components';
import { useState } from 'react';

const Component = styled.div`
  border: 3px solid green;
  border-radius: 10px;
  flex-grow: 1;
  line-height: 30px;
  text-align: center;
  margin: 10px;
  >button{
    margin-left: 10px;
  }
`;

const Container = styled.div`
  display: flex;
  width: 100%;
  justify-contents: center;
`;

export default function App() {
  const [greeting, setGreeting] = useState('Hello');

  console.log('App');
  return (
    <Container>
      <Component>
        App
        <Container>
          <Child1 greeting={greeting} setGreeting={setGreeting} />
          <Child2 greeting={greeting} setGreeting={setGreeting} />
        </Container>
      </Component>
    </Container>
  );
}

function Child1({ greeting, setGreeting }) {
  console.log('Child1');
  return (
    <Component>
      Child1
      <Container>
        <Child3 greeting={greeting} setGreeting={setGreeting} />
        <Child4 />
      </Container>
    </Component>
  );
}

function Child2({ greeting, setGreeting }) {
  console.log('Child2');
  return (
    <Component>
      Child2
      <Container>
        <Child5 />
        <Child6 greeting={greeting} setGreeting={setGreeting} />
      </Container>
    </Component>
  );
}

function Child3({ greeting, setGreeting }) {
  console.log('Child3');
  return <Component>Child3 : {greeting} </Component>;
}

function Child4() {
  console.log('Child4');
  return <Component>Child4</Component>;
}

function Child5() {
  console.log('Child5');
  return <Component>Child5</Component>;
}

function Child6({ greeting, setGreeting }) {
  console.log('Child6');
  return (
    <Component>
      Child6
      <button onClick={() => setGreeting(greeting + '!')}>👋</button>
    </Component>
  );
}
```


- 라이브러리 사용 예시
- [StackBlitz로 보기](https://stackblitz.com/edit/react-ts-szkjfd?file=App.js){: target="_blank"}



```jsx
import * as React from 'react';
import './style.css';
import styled from 'styled-components';
import { useSelector, useDispatch } from 'react-redux';

const Component = styled.div`
  border: 3px solid green;
  border-radius: 10px;
  flex-grow: 1;
  line-height: 30px;
  text-align: center;
  margin: 10px;
  >button{
    margin-left: 10px;
  }
`;

const Container = styled.div`
  display: flex;
  width: 100%;
  justify-contents: center;
`;

export default function App() {
  console.log('App');
  return (
    <Container>
      <Component>
        App
        <Container>
          <Child1 />
          <Child2 />
        </Container>
      </Component>
    </Container>
  );
}

function Child1() {
  console.log('Child1');
  return (
    <Component>
      Child1
      <Container>
        <Child3 />
        <Child4 />
      </Container>
    </Component>
  );
}

function Child2() {
  console.log('Child2');
  return (
    <Component>
      Child2
      <Container>
        <Child5 />
        <Child6 />
      </Container>
    </Component>
  );
}

function Child3() {
  const greeting = useSelector((state) => state);
  console.log('Child3');
  return <Component>Child3 : {greeting} </Component>;
}

function Child4() {
  console.log('Child4');
  return <Component>Child4</Component>;
}

function Child5() {
  console.log('Child5');
  return <Component>Child5</Component>;
}

function Child6() {
  console.log('Child6');
  const dispatch = useDispatch();
  const addBang = () => {
    dispatch({ type: 'AddBang' });
  };
  return (
    <Component>
      Child6
      <button onClick={addBang}>👋</button>
    </Component>
  );
}
```