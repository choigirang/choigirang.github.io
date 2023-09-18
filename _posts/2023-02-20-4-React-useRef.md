---
layout: post
title: React 11장 - useRef
author: admin
date: 2023-02-20 00:00:00 +900
lastmod: 2023-02-20 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]  # 대문자로 작성
tags: [react, components, useref]
---
# React
## useRef
- `useState`,`useEffet` 등을 통해 거의 대부분의 프론트엔드 요구사항을 구현할 수 있었지만, 모든 기능을 구현할 순 없다.
- DOM 엘리먼트의 주솟값을 활용해야 하는 경우 `useRef`로 DOM노드, 엘리먼트, React 컴포넌트의 주솟값을 참조하여야 한다.
  - focus
  - text selection
  - media playback
  - 애니메이션 적용
  - d3d.js , greensock 등 DOM 기반 라이브러리 활용
- 제시된 상황을 제외한 대부분의 경우 기본 React문법을 벗어나 `useRef`를 남용하는 것은 부적절하고, React의 특징이자 장점인 선언형 프로그래밍 원칙과 배치되기 때문에 조심해서 사용해야 한다.

#### 예시
- 주솟값 자체는 컴포넌트가 re-render 되더라도 바뀌지 않는다.
- 이러한 특성을 활용하여 `useRef`를 사용한다.
- `inputEl`에 참조자료형의 주솟값을 담고, 주소값을 담은 변수를 `props`로 담으면 `input DOM` 엘리먼트의 주소가 담긴다.
- 다른 컴포넌트에서 `input DOM` 엘리먼트를 활용할 수 있다.



```jsx
function TextInputWithFocusButton(){
    const inputEl = useRef(null);
    const onButtonClick = () => {
        inputEl.current.focus()
    }

    return (
        <>
            <input ref={inputEL} type="text" />
            <button onClick={onButtonClick}>Focus the input</button>
        </>
    )
}
```


#### 예시 2
- [sandbox 예제보기](https://codesandbox.io/s/priceless-sanderson-kx77s?from-embed){: target="_blank"}
- `useRef`를 통해 각 요소(input)의 주솟값을 담고있다.
- `input`요소에 타이핑을 할 때마다 `handleInput`함수가 실행되는데 모든 값을 입력 후 `Enter`를 누르면, `handleInput`함수의 첫 번쨰 `if`문의 조건과 맞아진다.
- `handleInput`은 `event`를 인자로 받는데, `event`는 `handleInput`을 발생시킨 `input`요소를 가르킨다.
- `input`에 입력된 `key`가 `Enter`일 경우에는 아래의 `if`조건문을 실행한다.
  - 세 번째까지 모두 작성 후 `Enter`를 눌렀다면, 현재의 입력창은 세 번째 `input`요소이다.
  - 세 번째 `if`문의 조건 `event.target === thirdRef.current` 이라면 (이벤트를 발생시킨 요소가 `thirdRef`(세 번째 `input`)) 첫 번째 요소의 주솟값을 담고있는 `firstRef`를 `focus`해주고, `thirdRef`의 값을 빈 값으로 만들어준다.
  - 이러한 과정이 반복되어 `Enter`를 반복했을 때에 모든 `input` 안에는 공백만 남는다.



```jsx
import React, {useRef} from "react"

const Focus = () => {
    const firstRef = useRef(null);
    const secondRef = useRef(null);
    const thirdRef = useRef(null);

    const handleInput = (event) => {
        if(event.key === "Enter"){
            if(event.target === firstRef.current){
                secondRef.current.focus();
                event.target.value = "";
            }else if(event.target === secondRef.current){
                thirdRef.current.focus();
                event.target.value = "";
            }else if(event.target === thirdRef.current){
                firstRef.current.focus();
                event.target.value = "";
            }else{
                return
            }
        }
    }
}

return (
    <div>
        <h1>타자연습</h1>
        <h3>각 단어를 바르게 입력하고 엔터를 누르세요.</h3>
        <div>
            <label>Hello</label>
            <input ref={firstRef} onKeyUp={handleInput} />
        </div>
        <div>
            <label>there</label>
            <input ref={secondRef} onKeyUp={handleInput} />
        </div>
        <div>
            <label>here</label>
            <input ref={thirdRef} onKeyUp={handleInput} />
        </div>
    </div>
)

export default Focus;
```


#### 예시 3
- [sandbox 예제보기](https://codesandbox.io/s/priceless-sanderson-kx77s?from-embed=&file=/src/App.js){: target="_blank"}
- 비디오의 주솟값을 `videoRef`에 담는다.
- 각각 Play,Pause `button`이 존재하는데, 버튼을 클릭 시 `playVideo`,`pauseVideo`함수가 실행되는데, `videoRef`를 `play()`,`pasue()`를 한다.
- `button`에 의해 `video`가 재생되거나 멈춘다.



```jsx
import {useRef} from "react"

export default function App(){
    const videoRef = useRef(null);

    const playVideo = () => {
        videoRef.current.play();
    }

    const pauseVideo = () => {
        videoRef.current.pause();
        videoRef.current.remove();
    }
}

return (
    <div>
        <div>
            <button onClick={playVideo}>Play</button>
            <button onClick={pauseVideo}>Pause</button>
        </div>
        <video ref={videoRef} width="320" height="240" constrols>
            <source type="video/mp4" src="https://예제.mp4" />
        </video>
    </div>
)
```