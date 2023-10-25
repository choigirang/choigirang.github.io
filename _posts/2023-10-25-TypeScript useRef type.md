---
layout: post
title: TypeScript 12장 - useRef 사용하기
author: admin
date: 2023-10-25 00:00:00 +900
lastmod: 2023-10-25 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [TYPESCRIPT]
tags: [typescript, javascript, react, useref]
---

# TypeScript

## useRef

[useRef 공식문서](https://ko.legacy.reactjs.org/docs/hooks-reference.html#useref)

- React hook의 일종으로, 인자로 넘어온 초깃값을 `useRef`객체의 `.current` 프로퍼티에 저장한다.
- DOM객체를 직접 가리킬 때나, 값이 변경되어도 컴포넌트가 리렌링되지 않도록 하기 위해 값들을 저장할 때도 사용한다.
- `.current`를 변경시키는 것은 리렌더링을 발생시키지 않기 때문에 로컬 변수 용도로도 사용할 수 있다.

### 타입 에러

[참고자료 1](https://darrengwon.tistory.com/865)
[참고자료 2](https://driip.me/7126d5d5-1937-44a8-98ed-f9065a7c35b5)

- 미니 프로젝트를 만들던 중 `useRef`에 대한 타입에러가 발생했다.
- 검색어를 입력하는 `input`을 만들고, 그 옆에 검색 아이콘 버튼을 만들어서, 엔터를 쳤을 때나 아이콘을 클릭했을 때 검색어가 저장되게 해주고 싶었다.
- `useRef`를 사용해서 `input`에 입력된 현재값을 가져오고 싶었는데 `input props type error`가 발생했다.

```tsx
function Example(){
    const [keyword, setKeyword] = useState("");

    const inputRef = useRef();

    const keywordHandler: ComponentProps<"input">["onKeyPress"] = (e) => {
    if (e.key === "Enter") {
      const inputElement = e.target as HTMLInputElement;
      setKeyword(inputElement.value);
    }
  };

  const iconClickHandler: ComponentProps<"svg">["onClick"] = () => {
    const inputValue = inputRef.current.value;
    setKeyword(inputValue);
  }

  return (
    <React.Fragment>
        <InputEle onKeypress={keywordHandler} {/*에러 부분*/} ref={inputRef}/>
        <AiFillSearch onClick={}>
    </React.Fragment>
  )
}
```

```
'MutableRefObject<HTMLInputElement | null | undefined>' 형식은 'LegacyRef<HTMLInputElement> | undefined' 형식에 할당할 수 없습니다.
'MutableRefObject<HTMLInputElement | null | undefined>' 형식은 'RefObject<HTMLInputElement>' 형식에 할당할 수 없습니다.
'current' 속성의 형식이 호환되지 않습니다.
'HTMLInputElement | null | undefined' 형식은 'HTMLInputElement | null' 형식에 할당할 수 없습니다.
'undefined' 형식은 'HTMLInputElement | null' 형식에 할당할 수 없습니다.
```

### 해결 방법

- `useRef`에 초깃값을 `null`로 설정해주면 간단하게 해결할 수 있다.

```tsx
const inputRef = useRef(null);
```

### 원인

- `useRef`를 `ctrl + 클릭`으로 타입을 확인해보면 3가지 타입을 확인할 수 있다.

```jsx
function useRef<T>(initialValue: T): MutableRefObject<T>;
function useRef<T>(initialValue: T | null): RefObject<T>;
function useRef<T = undefined>(): MutableRefObject<T | undefined>;

interface MutableRefObject<T> {
        current: T;
}

interface RefObject<T> {
        readonly current: T | null;
    }
```

- 설명에 대한 경우의 수는 다음과 같다.
  1. `useRef`에 입력될 **인자의 타입과 제네릭 타입이 일치할 경우**,`MutableRefObject<T>`
  2. `useRef`에 입력될 **인자의 타입이 null을 허용한 경우**,`RefObject<T`
  3. `useRef`에 입력될 **제네릭 타입이 undefined인 경우**,`MutableRefObject<T | undefined>`
- 각 경우의 수에 해당하는 `MutableRefObject, RefObject`를 보면 `MutableRefObject`은 **초깃값을 current에 저장하는 반면**, `RefObject`의 값은 **읽기 전용**으로 되어 있다.
- **MutableRefObject는 current 값을 변경할 수 있고, RefObject의 current 값은 변경될 수 없다.**

#### 예시 1.

- 버튼을 클릭하여 값을 증가시키는 코드이다.
- `current`를 직접 변경시킬 수 있는 이유는, `useRef`의 **초기 인자를 입력한 1번의 경우에 해당한다.**

```tsx
import React, { useRef } from "react";

function App() {
  const localVarRef = useRef<number>(0);

  const handleButtonClick = () => {
    if (localVarRef.current) {
      localVarRef.current += 1;
      console.log(localVarRef.current);
    }
  };

  return (
    <div className="App">
      <button onClick={handleButtonClick}>+1</button>
    </div>
  );
}

export default App;
```

#### 예시 2.

- 만약 초기 인자로 `null`을 넘겨주면, **2번 경우에 해당되기 때문에 currrent에 에러가 발생하게 된다.**

```tsx
const localVarRef = useRef<number>(null);

const handleButtonClick = () => {
  localVarRef.current += 1;
  console.log(localVarRef.current); // 에러발생
};
```

```
React.RefObject<number>.current : number | null
Cannot assign to 'current' because it is a read-only property.
```

#### 예시 3.

- 만약 버튼을 클릭하여 current의 value값을 변경하고자 할 때, 초깃값을 null로 지정했을 때의 예시이다.
- **초깃값을 null로 넘겨줬으니 2번의 경우에 해당하지만 value 값을 변경할 수 있는데, 이는 cuurent 프로퍼티만 읽기 전용으로 하위 프로퍼티인 value 는 수정이 가능하기 때문이다.**

```tsx
import React, { useRef } from "react";

function App() {
  const inputRef = useRef<HTMLInputElement>(null);

  const handleButtonClick = () => {
    if (inputRef.current) {
      inputRef.current.value = "";
    }
  };

  return (
    <div className="App">
      <button onClick={handleButtonClick}>+1</button>

      <input ref={inputRef} />
      <button onClick={handleButtonClick}>Clear</button>
    </div>
  );
}

export default App;
```

#### 예시 4.

- 그럼 `useRef`의 인자를 `undefined`로 바꿀 경우를 봐보자.
- 에러 메시지를 보면 `MutableRefObject`형식은 `RefObject` 형식에 할당할 수 없다는 메시지를 볼 수 있다.

```tsx
const inputRef = useRef<HTMLInputElement>();
```

```
'MutableRefObject<HTMLInputElement | undefined>' 형식은 'RefObject<HTMLInputElement>' 형식에 할당할 수 없습니다.
'current' 속성의 형식이 호환되지 않습니다.
'HTMLInputElement | undefined' 형식은 'HTMLInputElement | null' 형식에 할당할 수 없습니다.
'undefined' 형식은 'HTMLInputElement | null' 형식에 할당할 수 없습니다.
오버로드 2/2('(props: FastOmit<DetailedHTMLProps<InputHTMLAttributes<HTMLInputElement>, HTMLInputElement>, never>): ReactElement<...> | null')에서 다음 오류가 발생했습니다.
'MutableRefObject<HTMLInputElement | undefined>' 형식은 'LegacyRef<HTMLInputElement> | undefined' 형식에 할당할 수 없습니다.ts(2769)
```
