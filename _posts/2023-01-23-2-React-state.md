---
layout: post
title: React 4장 - state
author: admin
date: 2023-01-23 00:00:00 +900
lastmod: 2023-01-23 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]  # 대문자로 작성
tags: [react, 리액트, map, jsx, usestate, event, state, prop]
---
# React
## state
- `prop`가 컴포넌트에 매개변수로 전달되는 역할을 한다면, `state`는 컴포넌트에 대한 데이터 또는 정보를 포함하는 데 쓰이는 **객체**이다.
- 저장된 `state`는 값이 바귈 때마다 컴포넌트를 다시 렌더링시킨다.
- `state`는 여러 속성을 지정할 수 있으며 `useState`를 통해 값을 변경(추가,삭제)시키기도 한다.
- 비동식이다.
- `react`에서 `useState`를 호출해서 사용한다.


#### 예시
- `App` 컴포넌트 안에 각각 `blue`,`red`의 상태를 지정한다.
- `useState`에 전달되는 값은 `blue`와`red`의 초기값을 의미한다.
- `setState`는 `blue`와`red`의 상태를 변경한다.
- `button`이 클릭될 떄마다 각각 함수를 실행시키는데, 함수 안에는 `state`를 변경시킨다.
- 함수 안에 `blue++`,`red++`라고 작성 시, 변수 자체를 변경하는 것으로 취급하여 `const`에 대한 재할당이 실행된다.
- 따라서, `blue+1`,`red+1`과 같이 작성해주어야 한다.
- `button`에 전달된 `onClick`은 리액트의 이벤트핸들링으로 클릭했을 때의 실행을 의미한다.


```jsx
import React, {useState} from "react"

function App (){
    const [blue, setBlue] = useState(0)
    const [red, setRed] = useState(0)

    function bluePlus(){
        setBlue(blue+1)
    }

    function redPlus(){
        setRed(red+1)
    }

    return (
        <div>
            <div>
                <h3>파란색 버튼 클릭 횟수 : {blue}</h3>
                <button onClick={bluePlus}>블루 버튼 증가시키기</button>
            </div>
            <div>
                <h3>레드 버튼 클릭 횟수 : {red}</h3>
                <button onClick={redPlus}>레드 버튼 증가시키기</button>
            </div>
        </div>
    )
}
```

#### 예시 2
- 입력한 글자를 보여주는 기능 구현
- 보여줄 텍스트의 상태를 지정한다. => `useState`
- `text`의 초기값은 아무것도 지정하지 않은 공백이다.
- `input`버튼을 만들고 버튼의 `prop` 중 `value`는 값을 의미하며 `input`에 입력된 텍스트를 가르킨다. 입력되었을 때 실행되는 이벤트핸들러인 `onChange`를 작성한다.
- `onChange`에 의해 값이 입력될 때마다 `inputValue`함수를 실행시키는데, `input`에 입력되는 값을 `e` 객체를 전달받는다.
- `e`안에 `target`이라는 메서드를 통해 이벤트가 발생된 대상을 지칭하며 `value`는 발생된 대상의 값을 의미한다.
    - 대상의 값은, 이벤트가 발생한 대상 `input`의 `prop` 중 `value`


```jsx
import React, {useState} from "react"

function App(){
    const [text, setText] = useState('')

    function inputValue(e){
        setText(e.target.value)
    }

    return (
        <div>
            <input type="text" value={text} onChange={inputValue}>
            <p>{text}</p>
        </div>
    )
}
```

#### 예시 3
- 삼항연산자를 이용한 상태 변경
- `changeText`의 초기값은 'React'이다.
- `button` 안에서 `onClick`이라는 이벤트핸들러가 발생 시, 바로 함수를 실행시키는데, 이 함수는 `changeText`값이 'React'가 맞다면 'Java'라는 값으로 바꾸고, 'React'가 아니라면 'React'로 상태를 바꿔준다`setChangeText`.


```jsx
import React,{useState} from "react"

function App(){
    const [changeText, setChangeText] = useState("React")

    return(
        <div>
            Change Mode {changeText}
            <button onClick={
                () => changeText === "React"? setChangeText("Java") : setChangeText("React")
            }>change!</button>
        </div>
    )
}
```