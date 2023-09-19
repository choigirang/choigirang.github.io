---
layout: post
title: React 37장 - recoil (1)
author: admin
date: 2023-03-23 00:00:00 +900
lastmod: 2023-03-23 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, recoil, state]
---

# React

## Recoil

- 상태 관리 라이브러리며, Facebook의 엔지니어가 개발했지만 리액트 상태 관리를 위한 공식적인 라이브러리는 아니다.
- 리액트 애플리케이션의 모든 구성 요소가 상태를 쉽게 공유할 수 있도록 전역 상태를 제공하며 Redux에 비해 최소화된다.

### 장점

- Redux를 사용할 때처럼 초반 설정을 위한 복잡한 코드 작성이 필요치 않다.
- 리액트 공식 라이브러리는 아니지만 페이스북에서 만들어져 리액트와 호환이 잘 된다.

### Recoil의 주요 개념

#### Root

- 상태를 사용하는 컴포넌트는 부모 트리 어딘가에 나타나는 RecoilRoot가 필요하다.
- 루트 컴포넌트가 RecoilRoot를 넣기에 가장 좋은 장소다.
- 리코일을 사용하려는 컴포넌트를 RecoilRoot로 감싸준다.
  ```jsx
  const root = ReactDOM.createRoot(document.getElementById("root"));
  root.render(
    <>
      <React.StrictMode>
        <RecoilRoot>
          <App />
        </RecoilRoot>
      </React.StrictMode>
    </>
  );
  ```

#### Atom

- Atom은 상태의 일부를 나타낸다.
- Atoms는 어떤 컴포넌트에서나 읽고 쓸 수 있다.
- atom의 값을 읽는 컴포넌트들은 암묵적으로 atom을 구독한다.
- atom의 어떤 변화가 있으면 그 atom을 구독하는 모든 컴포넌트들이 재 렌더링 되는 결과가 발생한다.

  - key는 고유한 key값
  - default는 atom의 초기값을 정의한다.

  ```jsx
  // const [open, setOpen] = useState(false)가 있을 때
  // default값이 초기값인 false와 비슷하다.

  export const textState = atom({
    key: "textState",
    default: "",
  });

  function App() {
    return (
      <>
        <div>
          <TextInput />
          <CharacterCount />
        </div>
      </>
    );
  }
  ```

### useRecoilState()

- 컴포넌트가 atom을 읽고 쓰게 하기 위해서는 `useRecoilState()`를 사용하면 된다.

  ```jsx
  import {atom, useRecoilState} from "recoil";

  export const textState = atom({
    key: "text",
    default: "".
  });

  function App(){
    const [text, setText] = useRecoilState(textState)
  }
  ```

### Selector

- atom 혹은 다른 Selector 상태를 입력받아 동적인 데이터를 반환하는 순수 함수이다.
- Selector가 참조하던 다른 상태가 변경되면 이도 같이 업데이트되며, 이때 Selector를 바라보던 컴포넌트들이 리 렌더링 되는 것이다.
- key는 고유한 값을 가진다.
- get은 Selector의 순수 함수로, 사용할 값을 반환하며, 매개변수인 콜백 객체 내 get()메서드로 atom 혹은 selector를 참조한다.

  ```jsx
  export const charCountState = selector({
    key: 'charCountState',
    get: ({get} => {
        // 매개변수인 get은 callback 내 객체
        const text = get(textState);
        // 우리가 text를 입력하면 "hello"
        // "hello"가 text로 들어오게 된다.

        return text.length
    })
  })

  function App(){
    return <>
        <div style=({padding: 16}) />
        <TextInput />
        <CharacterCount />
    </>
  }
  ```
