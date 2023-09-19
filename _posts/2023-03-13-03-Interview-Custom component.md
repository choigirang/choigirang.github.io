---
layout: post
title: Interview 11장 - Custom Component와 useRef
author: admin
date: 2023-03-13 00:00:00 +900
lastmod: 2023-03-13 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [INTERVIEW]
tags: [styled-components, useRef]
---

# Interview

## styled component의 장점

- 스타일에 대한 고유 클래스명을 생성하여 중복이나 오타 걱정이 없다.
- 페이지에서 렌더링되는 요소에 맞춰 해당 스타일을 삽입하기 때문에, 상황마다 필요한 스타일을 로드한다.
- 동적 스타일링이 편해지는데, props를 받아 개별적인 스타일링이 가능하다.

## useRef

- React에서 DOM을 직접 선택해야 하는 상황이 발생할 때 useRef로 DOM노드, 엘리먼트, 컴포넌트 주소값을 참조하여 엘리먼트 크기를 가져오거나, 스크롤바 위치를 가져오거나 설정하거나, 포커스를 설정하는 등에 사용할 수 있다.
- 애니메이션을 직접 실행시킬 때도 사용된다.

  - 컴포넌트에 focus를 위치시킬 필요가 있는 경우, input에 값을 전부 입력한 후 첫 번째 칸으로 이동하는 경우가 있다.

    ```jsx
    import React, { useState, useRef } from "react";

    function InputTest() {
      const [text, setText] = useState("");
      const nameInput = useRef();

      const onChange = (e) => {
        setTextr(e.target.value);
      };

      const onReset = (e) => {
        setText("");
        nameInput.current.focus();
      };

      return (
        <div>
          <input nane="name" onChange={onChange} value={text} ref={nameInput} />

          <button onClick={onReset}>초기화</button>
          <div>
            <b>내용: </b>
            {text}
          </div>
        </div>
      );
    }

    export default InputTest;
    ```

  - 속성 값을 초기화할 필요가 있는 경우, 카운터의 값을 0으로 초기화 해주는 타이머를 만들 때 setInterval이나 setTimeout과 같은 함수를 clear해주지 않으면 메모리를 많이 소모한다.

    ```jsx
    const RSPfunction = () => {
      const [result, setResult] = useState("");
      const [ingCoord, setImgCoord] = useState(rspCoords.바위);
      const [score, setScore] = useState(0);
      const interval = useRef();

      useEffect(() => {
        interval.current = setInterval(changed, 100);
        return () => {
          clearInterval(interval.current);
        };
      }, [imgCoord]);
    };
    ```
