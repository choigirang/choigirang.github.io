---
layout: post
title: React 6장 - Lifting State Up
author: admin
date: 2023-02-02 00:00:00 +900
lastmod: 2023-02-02 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]  # 대문자로 작성
tags: [router, props, compoents, useeffect]
---
# React
## 상태끌어올리기
- 리액트에서의 데이터는 위에서 아래로 흐르는데, 이를 데이터 하향식 흐름이라고 한다.
- 데이터는 단방향으로 흐르는데, 상위 컴포넌트에서 하위 컴포넌트로 `state`를 전달할 때 `props`를 통해 하위 컴포넌트가 이를 전달받고, `prop`이 어디서 왔는지 쉽게 알 수 있다.
- 하나의 `state`를 두 개의 컴포넌트가 사용한다고 했을 때, `state`를 상위 컴포넌트에 위치시켜 데이터의 흐름을 지켜야 한다.
- 상태의 위치를 상위 컴포넌트에 위치시켰고 하위 컴포넌트에 의해 상위 컴포넌트의 상태가 변하는 것을 상태끌어올리기라고 한다.
- 콜백 함수를 호출하듯이, 상태를 변경시키는 함수 자체를 하위 컴포넌트에 `prop`로 전달한다.

#### 예시
- 상위 컴포넌트에 `state`와 `state`를 바꿔줄 함수를 작성한다.
- 작성한 함수를 하위 컴포넌트에 `prop`으로 전달한다.


- Parents 컴포넌트
    - 상위 컴포넌트에서 `state`를 작성한다.
    - 함수를 실행시키면 `state`를 변경시키는데, 실행 함수를 하위 컴포넌트인 Child 컴포넌트의 `prop`으로 전달한다.



```jsx
import React, {useState} from "react"
import Child from "./Child.js"

function Parents(){
    const [changeValue, setChangeValue] = useState("처음은 부모의 값")
    
    function changeValueWithChild(){
        setChangeValue("자식이 값 바꿔주기")
    }

    return (
        <div>
            <Child valueFunc={changeValueWithChild} />
        </div>
    )
}
```


- Child 컴포넌트
  - Parents 컴포넌트로부터 `valueFunc`를 `prop`으로 전달받는다.
  - 전달받은 `valueFunc`는 Parent 컴포넌트에서 작성된 `changeValueWithChild` 함수를 담고 있다.
  - Child 컴포넌트의 `button`을 누르면 `parentsFunctionGo` 함수를 실행시키는데, 이 함수는 전달받은 `prop`인 `valueFunc`를 실행시킨다.
    - Parents 컴포넌트에 전달되어 최종적으로 `changeValueWithChild` 함수를 실행시킨다.
  - Parents 컴포넌트의 `state` 값이 Child 컴포넌트에 의해 `"자식이 값 바꿔주기"` 로 변경된다.



```jsx
import React from "react"

function Child({valueFunc}){
    function parentsFunctionGo(){
        valueFunc()
    }

    return (
        <button onClick={parentFunctionGO}>부모 값 변경</div>
    )
}
```



#### 예시 2
- 콜백 함수의 인자로 전달하기
- Parents 컴포넌트



```jsx
import React,{useState} from "react"
import Child from "./Child.js"

function Parents(){
    const [changeValue, setChangeValue] = useState("하위 컴포넌트의 값이 올 자리")

    function changeValueWithChild(newValue){
        setChangeValue(newValue)
    }

    return (
        <div>
            <Child valueFunc={changeValueWithChild} />
        </div>
    )
}
```



- Child 컴포넌트



```jsx
import React from "react"

function Child({valueFunc}){
    function parentsFunctionGo(){
        valueFunc("여기에서 값을 변경한다")
    }
    return (
        <button onClick={}>부모 값 변경</button>
    )
}
```



#### 예시 3
- Twitter 컴포넌트
  - 작성하는 `tweet`이 계속 추가된다.
  - NewTweet, SingleTweet 컴포넌트는 Twitter 컴포넌트의 상태값을 공유한다.
  - `addTweetFunc`이 실행되면 기존에 작성된 `tweet`에 새로 작성한 `tweet`를 추가한다.
    - 하위 컴포넌트인 NewTweet에서 작성된 새로운 `tweet`은 인자인 `newTweet`으로 전달받는다.
  - 저장된 `tweet`는 SingleTweet의 `props`로 값을 전달해준다.



```jsx
import React,{useState} from "react"
import NewTweet from "./NewTweet.js"
import SingleTweet from "./SingleTweet.js"

function Twitter(){
    const[tweet, setTweet] = useState([
        {
            id : 1,
            writer : "John",
            date : "2022-02-20",
            content : "트위터 추가하기"
        },
        {
            id : 2,
            writter : "Jane"
            date : "2022-02-21",
            content : "트위터 기존 내용"
        }
    ])

    const addTweetFunc(newTweet){
        setTweet([...tweet, newTweet])
    }

    return (
        <div>
            <NewTweet addTweet={addTweetFunc} />
            <ul>
                {tweet.map((tweetFactor) => (
                    <SingleTweet key={tweetFactor.id} 
                    writer={tweetFactor.writer} 
                    date={tweetFactor.date}>{tweetFactor.content}</SingleTweet>
                ))}
            </ul>
        </div>
    )
}
```



- NewTweet 컴포넌트
  - 상위 컴포넌트인 Twitter에서 전달받은 `prop`인 `addTweet`은, 상위 컴포넌트에 작성된 함수인 `addTweetFunc`을 실행시킨다.
  - 각각 `input`,`textarea`에 값이 입력될 때마다 지정된 함수를 통해 `writer`,`textValue`의 `state`를 변경시킨다.
  - 변경된 `state`를 `goAddTweetFunc`함수에서 사용하며, 함수는 작성된 `newTweet`을 다시 상위 컴포넌트에서 작성된 `addTweetFunc`을 실행시키기 위해 전달된 `prop`인 `addTweet`자체를 실행시킨다.
    - 작성된 `tweet`은 상위 컴포넌트에 전달인자인 `newTweet`으로 넘겨준다.




```jsx
import React from "react"

function NewTweet({addTweet}){
    const [textValue, setTextValue] = useState("")
    const [writer, setWriter] = useState("")

    function writerChange(e){
        setWriter(e.target.value)
    }

    function textChange(e){
        setTextValue(e.target.value)
    }

    function goAddTweetFunc(){
        let newTweet = {
            id : Math.floor(Math.random()*10000),
            writer,
            date : new Date().toISOString().substring(0,10),
            content : textValue
        }
        addTweet(newTweet)
    }

    return (
        <form onSubmit={goAddTweetFunc}>
            <input type="text" onChange={writerChange}>
            <textarea onChange={textChange}></textarea>
            <button type="submit">새 글 쓰기</button>
        </form>
    )
}
```


- SingleTweet 컴포넌트
    - NewTweet 컴포넌트에서 작성된 새로운 `tweet`은 상위 컴포넌트인 Twitter에 추가되고 추가된 `tweet`의 상태를 SingleTweet 컴포넌트가 넘겨받아 이를 토대로 요소를 생성한다.



```jsx
import React from "react"

function SingleTweet({writer,date,content}){
    return(
        <li>
            <div>{writer}</div>
            <div>{date}</div>
            <div>{content}</div>
        </li>
    )
}
```