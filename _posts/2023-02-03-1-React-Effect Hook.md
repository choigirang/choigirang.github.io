---
layout: post
title: React 7장 - Effect Hook
author: admin
date: 2023-02-03 00:00:00 +900
lastmod: 2023-02-03 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]  # 대문자로 작성
tags: [react, usestate, useeffect, jsx, props]
---
# React
## Effect Hook
- `useEffect`는 컴포넌트 내에서 `Side Effect`를 실행할 수 있게 하는 Hook이다.
- `useEffect`는 두 가지 인자를 전달받는데, 첫 번째 인자는 함수이며 두 번째 인자는 언제 배열을 받아 언제 실행될지를 결정한다.
- 두 번째 인자로 전달받는 배열을 `dependency array`라고 하며, 배열에 입력된 특정값이 바뀔 때마다 첫 번째 인자인 함수를 실행한다.



```jsx
// 컴포넌트가 처음 생성되거나, props가 업데이트 되거나, state가 업데이트 될 때 실행
useEffect(()=>{
    console.log("첫 번째 인자를 실행합니다.")
})
```


```jsx
// 컴포넌트가 처음 생성될 때만 실행
useEffect(()=>{
    console.log("첫 번째 인자를 실행합니다.")
},[])
```


```jsx
// 특정값이 바뀔 때마다 실행
useEffect(()=>{
    console.log("첫 번째 인자를 실행합니다.")
},[dependency])
```


#### 예시
- `state`변경에 따른 경고창 띄우기



```jsx
import React,{useState,useEffect} from "react"

function Example(){
    const [count, setCount] = useState(0)
    function countUp(){
        setCount(count+1)
    }

    useEffect(()=>{
        if(count > 5){
            alert("최댓값은 5입니다.")
            setCount(5)
        }
    },[count])

    return (
        <div>
            <button onClick={countUp}>실행 횟수는{count}입니다.</button>
        </div>
    )
}
```


#### 예시 2
- `API`호출 시 로딩화면 띄우기
- Loading 컴포넌트



```jsx
import React from "react"

function Loading(){
    return <div>Loading</div>
}

export default Loading;
```
- App 컴포넌트
  - `loading`이 `true` 완료된 상태이면 화면에 보여주고, `false`이면 화면에서 보여지지 않게 처리한다.
  - `fetch`가 처리될 때는 `loading`의 상태를 `true`로 바꿔주고, `fetch`가 완료됐을 때는 `false`로 바꿔준다.
  - `API`호출이 완료되기 전까지는 Loading 컴포넌트가 보여진다.




```jsx
import React,{useState,useEffect} from "react"

function App(){
    const [loading,setLoading] = useState(true)

    const miniApi = async() =>{
        setLoading(true);
        try{
            const reponse = await fetch("api uri".{
                method: "POST",
                headers: {
                    Accept: "application/json",
                    "Context-Type":"application/json"
                },body:JSON.stringify(),
            })

            const result = await response.json();
            setLoading(false)
        }.catch(error){
            window.alert(error);
        }
    }

    useEffect(()=>{
        miniApi();
    },[])

    return (
        <div>
            {loading ? <Loading /> : null}
        </div>
    )
}
```