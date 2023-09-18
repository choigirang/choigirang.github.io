---
layout: post
title: React 17장 - ClickToEdit 기능
author: admin
date: 2023-02-22 00:00:00 +900
lastmod: 2023-02-22 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]  # 대문자로 작성
tags: [react, components, jsx, styled-components, clicktoedit]
---
# React
## ClickToEdit
- 값을 입력했을 때에 정해진 요소의 값을 입력한 값으로 바꿔주는 기능 구현

### 컴포넌트 짜기
- 입력할 컴포넌트와 입력을 출력할 컴포넌트로 나눈다.
- 나머지는 `styled-component`로 감싸준다.


```jsx
return (
    <>
        <InputView>
            <MyInput />
        </InputView>
        <InputView>
            <MyInput />
        </InputView>
        <InputView>
            <MyInput />
        </InputView>
    </>
)
```


### Styled-Components
- InputBox, InputEdit, InputView 컴포넌트로 하위 요소들을 정렬해준다.




```jsx
export const InputBox = Styled.div`
    text-align: center;
    display: inline-block;
    width: 150px;
    height: 30px;
    border: 1px #bbb dashed;
    border-radius: 10px;
    margin-left: 1rem;
`

export const InputEdit = styled.div`
    text-align: center;
    display: inline-block;
    width: 150px;
    height: 30px;
`

export const InputView = styled.div`
    text-align: center;
    align-items: center;
    margin-top: 3rem;

    div.view{
        margin-top: 3rem;
    }
`
```


### 기능 구현
#### 상태값 작성
- `useRef`로 요소를 선택한다.
- `isEditMode`로 값을 수정할 수 있는 상태를 만든다.
- `newValue`로 입력될 값을 지정한다.
- `useEffect`를 사용하여 `isEditMode`,`value`의 상태값이 변할 때마다 re-render 한다.


```jsx
const inputEl = useRef(null);
const [isEditMode, setEditMode] = useState(false)
const [newValue, setNewValue] = useState(value)

useEffect(()=>{
    if(isEditMode){
        inputEl.current.focus()
    }
}, [isEditMode])

useEffect(()=>{
    setNewValue(value)
},[value])
```


#### 컴포넌트에 이벤트 핸들러 부여
- `handleClick`함수로 입력하고 있는 상태를 알려주기 위해 `setEditMode`를 `true`와 `false`로 바꿔주는 역할을 한다.
- `handleBlur`함수로 마우스의 선택이 벗어났을 때 발생시키며, 마우스가 벗어난 상태를 저장하고 `setEditMode(false)` 입력한 값을 `newValue`의 상태로 바꾼다.
- `handleInputChange`함수로 `input`에서 입력한 `value`값을 `newValue`에 저장한다.


```jsx
const handleClick = () => {
    setEditMode(!isEditMode)
}

const handleBlur = () => {
    setEditMode(false)
    handleValueChange(newValue)
}

const handleInputChange = (e) => {
    setNewValue(e.target.value)
}
```


#### 코드
- InputBox 컴포넌트는 `div`를 갖고, InputEdit 컴포넌트는 `input`요소를, InputView 컴포넌트는 `div`를 갖는다.
- ClickToEdit 컴포넌트를 먼저 보면, ClickToEdit 컴포넌트는 InputView와 MyInput 두 가지 컴포넌트를 갖고 있다.
- InputView 컴포넌트는 `div` 요소를 갖고 MyInput 컴포넌트는 `value`값과 `handleValueChange`  함수를 인자로 받는다.
- MyInput 컴포넌트 안의 `handleValueChange` 함수는 `e`를 인자로 받는데, 이 인자는 ClickToEdit 컴포넌트에서 인자로 받은 `newValue => setName(newValue)`이다.
- `handleValueChange` 함수는 `setNewValue`를 통해 MyInput 컴포넌트의 `newValue`값을 ClickToEdit 컴포넌트의 MyInput으로 전달받은 입력값으로 상태를 변경한다.
- `useEffect`를 통해 re-render한다.
- ClickToEdit 컴포넌트의 MyInput 컴포넌트로 전달받은 `value` 인자는, 기존의 `name`값을 뜻하는데, MyInput 컴포넌트의 `newValue`는 `name`을 초기값으로 갖고 있다.
- 이제 MyInput 컴포넌트를 들여다보면 `isEditMode`가 거짓일 때, `span`태그를 보여주고 `newValue`를 갖고 있다.
  - `span`태그의 `newValue`는 초기값인, ClickToEdit 컴포넌트로부터 받은 `value`인 `name`이다.
- `span`태그를 클릭 시 `handleClick` 함수를 실행시킨다.
  - `handleClick` 함수는 `setEditMode`를 통해 `isEditMode`를 `true`값으로 바꾸는데,`useEffect`에 의해 `isEditMode`가 참이기 때문에 `inputEl`로 설정해 준 `input`태그를 `focus`해준다.
- `isEditMode`가 `true` 값으로 바뀌면 `return`문을 통해 InputEdit 컴포넌트를 갖게 된다.
- InputEdit 컴포넌트는 `input`태그를 갖고 있으며, `input`태그 안에 값을 입력할 때마다 `onChange={handleInputChange}`가 실행되어, `handleInputChange` 함수가 `newValue`상태값을 `setNewValue`를 통해 event를 발생시킨 현재의 `target`의 `value`값으로 바꿔준다.
- `input`태그가 아닌 다른 곳을 클릭할 시, focus가 풀리면서 이벤트 핸들러인 `onBlur`가 실행되고 `handleBlur`함수를 실행시킨다.
  - `handleBlur`에 의해 `isEditMode`의 상태는 `false`로 바뀌고 ClickToEdit 컴포넌트의 `handleValueChange`를 역으로 실행시켜 우리가 입력한 값을 `newValue`의 값으로 갖게 한다. 
  - `isEditMode`의 상태가 `false`로 바뀌었기 때문에 다시 `span`태그를 생성하여 입력할 `input`이 대체되게 된다.


```jsx
import {useState, useEffect, useRef} from "react"
import styled from "styled-components"

export const InputBox = styled.div`
    text-align: center;
    display: inline-block;
    width: 150px;
    height: 30px;
    border: 1px #bbb dashed;
    border-radius: 10px;
    margin-left: 1rem;
`

export const InputEdit = styled.input`
    text-align: center;
    display: inline-block;
    width: 150px;
    height: 30px;
`

export const InputView = styled.div`
    text-align: center;
    align-items: center;
    margin-top: 3rem;

    div.view{
        margin-top: 3rem;
    }
`

export const MyInput = ({value, handleValueChange}) => {
    const inputEl = useRef(null);
    const [isEditMode, setEditMode] = useState(false);
    const [newValue, setNewValue] = useState(value);

    useEffect(()=>{
        if(isEditMode){
            inputEl.current.focus();
        }
    },[isEditMode])

    useEffect(()=>{
        setNewValue(value)
    },[value])

    const handleClick = () => {
        setEditMode(!isEditMode)
    }

    const handleBlur = () => {
        setEditMode(false)
    }

    const handleInputChange = (e) => {
        setNewValue(e.target.value)
    }

    return (
        <InputBox>
            {isEditMode ? (
                <InputEdit
                    type="text"
                    value={newValue}
                    ref={inputEl}
                    onBlur={handleBlur}
                    onChange={handleInputChange}
                />
            ):(
                <span onClick={handleClick}>{newValue}</span>
            )}
        </InputBox>
    )
}

const cache = {
    name: "김코딩",
    age: 20
}

export const ClickToEdit = () => {
    const [name, setName] = useState(cache.name)
    const [age, setAge] = useState(cache.age)

    return (
        <>
            <InputView>
                <label>이름</label>
                <MyInput value={name} handleValueChange={(newValue)=> setName(newValue)} />
            </InputView>
            <InputView>
                <label>나이</label>
                <MyInput value={age} handleValueChange={(newAge)=> setName(newAge)} />
            </InputView>
            <InputView>
                <div className="view">이름{name}나이{age}</div>
            </InputView>
        </>
    )
}

```

### stories 작성
- 컴포넌트명과 `stories.js` 를 작성하면 컴포넌트의 `stories`로 인식한다.
- Tag 컴포넌트를 불러온다.
- 기본값의 `title`은 `Example`의 폴더 안에 ClickToEdit 컴포넌트를 의미한다.
- 기본값의 `component`는 ClickToEdit 컴포넌트를 지칭한다.
- `stories`는 storybook
- [stories 설명보기](https://choigirang.github.io/react/3-React-Storybook/){:target="_blank"}


```jsx
import React from "react";
import {ClickToEdit} from "./components/ClickToEdit.js"

export default{
    title: "Example/ClickToEdit",
    component: ClickToEdit
};

const Template = (args) => <ClickToEdit {...args} />;

export const Primary = Template.bind({});
Primary.args = {
    primary: true,
    label: "ClickToEdit"
}
```