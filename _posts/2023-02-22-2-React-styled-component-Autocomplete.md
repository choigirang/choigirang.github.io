---
layout: post
title: React 16장 - Autocomplete 기능
author: admin
date: 2023-02-22 00:00:00 +900
lastmod: 2023-02-22 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT] # 대문자로 작성
tags: [react, components, jsx, styled-components, autocomplete]
---

# React

## Autocomplete

- 갖고 있는 정보에 일치하는 문자를 입력했을 경우, 아래에 문자와 일치하는 정보를 나타내는 기능 구현

### 컴포넌트 짜기

- 입력창을 갖고 있는 컴포넌트와 입력을 받았을 때 하위에 자동완성 기능을 나타낼 컴포넌트로 나눈다.
- 컴포넌트 안에 요소를 작성한다.

```jsx
return (
    <InputContainer>
        <input />
    </InputContainer>
    <DropDown />
)
```

### Styled-Components

- 기존의 정보값을 갖고 있을 배열을 임의로 작성해준다.
- InputContainer에 `div`를 만들어 속성을 정한다.
  - `focus-within`은 `input`을 클릭했을 때 발생
- 상태값에 따라 클래스를 추가하고 빼내어 속성값을 부여한다.

```jsx
const autoValue = [
  "apple",
  "angle",
  "adios",
  "banana",
  "bare",
  "bread",
  "candy",
  "car",
  "drop",
  "down",
  "function",
];

const boxShadow = "0 4px 6px rgb(32 33 36 / 28%)";
const inactiveBorderRadius = "1rem 1rem 1rem 1rem";

export const InputContainer = styled.div`
  margin-top: 8rem;
  background-color: #ffffff;
  display: flex;
  felx-direction: row;
  padding: 1rem;
  border: 1px solid rgb(223, 225, 229);
  border-radius: ${inactiveBorderRadius};
  z-index: 3;
  box-shadow: 0;

  &:focus-within {
    box-shadow: ${boxShadow};
  }

  > input {
    flex: 1 0 0;
    background-color: transparent;
    border: none;
    margin: 0;
    padding: 0;
    outline: none;
    font-size: 16px;
  }

  > div.delete-button {
    cursor: pointer;
  }
`;

export const DropDownContainer = styled.ul`
    background-color: #ffffff;
    display: block;
    margin-left: auto;
    margin-right: auto;
    list-styled-type: none;
    margin-block-start: 0;
    margin-block-end: 0;
    margin-inline-start: 0;
    margin-inline-end: 0;
    padding-inline-start: 0p;
    margin-top: -1px;
    padding: 0.5rem 0;
    border: 1px solid rgb(223, 225, 229);
    border-radius: 0 0 1rem 1rem;
    box-shadow: ${boxShadow}
    z-index: 3;

    > li {
        padding: 0 1rem;
    }

    > li:hover {
        background-color: rgb(223,225,229);
    }

    &.select {
        background-color: rgb(223,225,229);
    }
`;
```

### 기능 구현

#### 상태값 작성

- 초기값은 기존에 작성된 `autoValue`배열을 갖는다.
- `hasText`는 `input`창에 값이 입력되었는지를 확인한다.
- `inputValue`는 배열에 `input`에 입력된 값을 추가하기 위해, `input`에 작성된 `value`를 의미한다.
- `options`는 초기에 작성한 `autoValue`값을 의미한다.
- `selectedOption`는 우리가 선택한 옵션에 따른 `index`를 가르킨다.
- `useEffect`를 사용하여 `input`에 입력한 값이 빈 배열이 아닐 때, 기존에 갖고있는 배열과 중복되지 않는다면 값을 추가하기 위해 `inputValue`의 상태값이 변경될 때마다 새롭게 렌더링한다.
  - `push`를 이용하면 기존에 갖고있는 배열의 주소값이 변하지 않아 리액트에서 상태의 변화를 알아차리지 못 하기 때문에 `filter`,`mpa`,`...`을 사용하여 추가한다.

```jsx
const [hasText, setHasText] = useState(false);
const [inputValue, setInputValue] = useState("");
const [options, setOptions] = useState(autoValue);
const [selectedOption, setSelectedOption] = useState(0);

useEffect(() => {
  if (inputValue === "") {
    setHasText(false);
  }
  if (inputValue !== "") {
    setOptions(autoValue.filter((option) => option.includes(inputValue)));
  }
}, [inputValue]);
```

#### 컴포넌트에 이벤트 핸들러 부여

- 값을 입력하거나, 자동완성을 선택하거나, 삭제버튼을 눌렀을 때에 발생할 이벤트를 작성한다.
- `handleInputChange`함수는 이벤트를 발생시킨 요소의 값을 그대로 받아 기존의 배열에 추가하는 역할을 하며, `setHasText`상태를 `true`로 만들어 `input` 값의 유무를 설정한다.
- `handleDropDownClick`함수는 우리가 선택한 자동완성된 값을 `input`요소의 `value`로 넣어주는 역할을 한다.
- `handleDeleteButtonClick`함수는 입력한 `input`창의 `value`값을 공백으로 만들어, 입력한 값을 삭제해주는 역할을 한다.
- `handleKeyUp`함수는 `input`을 작성한 상태에서 자동완성된 값을 선택해 주기 위해 실행되며, `ArrowDown`은 화살표 아래 키를 의미하며, `ArrowUp`은 화살표 위 키를 눌렀을 때에 `index`에 값을 추가하거나 빼서 `index`값을 설정해준다.
  - 작성 후 `Enter`를 누른다면 `handleDropDown`함수로 전달인자를 넘겨 기존 배열의 `index`를 선택한다.
  - 선택 후에는 `setSelectedOption`을 0으로 만들어, 0번째 `index`값을 갖게 한다.

```jsx
const handleInputChange = (event) => {
  setInputValue(event.target.value);
  setHasText(true);
};

const handleDropDownClick = (clickedOption) => {
  setInputValue(clickedOption);
};

const handleDeleteButtonClick = () => {
  setInputValue("");
};

const handleKeyUp = (event) => {
  if (event.key === "ArrowDown" && selectedOption < options.length - 1) {
    setSelectedOption(selectedOption + 1);
  }
  if (event.key === "ArrowUp" && selectedOption > 0) {
    setSelectedOption(selectedOption - 1);
  }
  if (event.key === "Enter") {
    handleDropDownClick(options[selectedOption]);
    setSelectedOption(0);
  }
};
```

#### 코드

- 적절한 요소에 이벤트 핸들러를 부여한다.
- `input` 요소에 입력된 값을 뜻하는 `value`는 `inputValue`인 상태값이다.
  - `input`에 값이 입력되면 `handleInputChange`함수가 실행되는데, 실행되는 함수는 `input`에 입력된 `value`값으로 상태값이 변한다.
    - 함수의 실행 형태로 전달하면 안된다.
    - 키가 입력됐을 때, 키를 판별하여 자동완성의 `index`를 구하기 위해 `selectedOption`값이 변경된다.
  - `delete-button`을 누를 때, `handleDelelteButtonClick` 함수를 실행시키는데, 입력된 값을 초기화한다.
  - 자동완성 기능을 가진 DropDown 컴포넌트는 DropDownContainer 컴포넌트를 갖고 있으며, 기존 값을 갖고 있는 배열 `options`, 선택한 `index`의 값에 클래스를 부여할 `selectedOption`을 인자로 넘겨준다..
    - DropDownContainer 컴포넌트는 `li`요소에 `options`의 각각의 값과 `index`를 갖고, `option`을 `handleComboBox`가 가진 `handleDropDownClick`함수로 전달인자를 넘겨주어 선택한 `option`요소를 `input`창에 넣어준다.

```jsx
import {useState,useEffect} from "react"
import styled from "styled-components"

const autoValue = [
    "apple",
    "angle",
    "adios",
    "banana",
    "bare",
    "bread",
    "candy",
    "car",
    "drop",
    "down",
    "function"
]

const boxShadow = "0 4px 6px rgb(32 33 36 / 28%)";
const inactiveBorderRadius = "1rem 1rem 1rem 1rem";

export const InputContainer = styled.div`
    margin-top: 8rem;
    background-color: #ffffff;
    display: flex;
    felx-direction: row;
    padding: 1rem;
    border: 1px solid rgb(223, 225, 229);
    border-radius: ${inactiveBorderRadius};
    z-index: 3;
    box-shadow: 0;

    &:focus-within {
        box-shadow: ${boxShadow};
    }

    > input {
        flex: 1 0 0;
        background-color: transparent;
        border: none;
        margin: 0;
        padding: 0;
        outline: none;
        font-size: 16px;
    }

    > div.delete-button {
        cursor: pointer;
    }
`

export const DropDownContainer = styled.ul`
    background-color: #ffffff;
    display: block;
    margin-left: auto;
    margin-right: auto;
    list-styled-type: none;
    margin-block-start: 0;
    margin-block-end: 0;
    margin-inline-start: 0;
    margin-inline-end: 0;
    padding-inline-start: 0p;
    margin-top: -1px;
    padding: 0.5rem 0;
    border: 1px solid rgb(223, 225, 229);
    border-radius: 0 0 1rem 1rem;
    box-shadow: ${boxShadow}
    z-index: 3;

    > li {
        padding: 0 1rem;
    }

    > li:hover {
        background-color: rgb(223,225,229);
    }

    &.select {
        background-color: rgb(223,225,229);
    }
`

export const Autoncomplete = () => {
    const [hasText, setHasText] = useState(false);
    const [inputValue, setInputValue] = useState("");
    const [options, setOptions] = useState(autoValue)
    const [selectedOption, setSelectedOption] = useState(0)

    useEffect(()=>{
        if(inputValue === ""){
            setHasText(false);
        }
        if(inputValue !== ""){
            setOptions(
                autoValue.filter((option)=>option.includes(inputValue))
            )
        }
    },[inputValue])


    const handleInputChange = (event) => {
        setInputValue(event.target.value);
        setHasText(true);
    }

    const handleDropDownClick = (clickedOption) => {
        setInputValue(clickedOption);
    }

    const handleDeleteButtonClick = () => {
        setInputValue("")
    }

    const handleKeyUp = (event) => {
        if(event.key === "ArrowDown" && selectedOption < options.length - 1){
            setSelectedOption(selectedOption + 1);
        }
        if(event.key === "ArrowUp" && selectedOption > 0){
            setSelectedOption(selectedOption - 1);
        }
        if(event.key === "Enter"){
            handleDropDownClick(options[selectedOption])
            setSelectedOption(0);
        }
    }

    return (
        <div>
            <InputContainer>
                <input type="text"
                value={inputValue}
                onChange={(event)=>{
                    handleInputChange(event);
                }}
                onKeyUp={(event)=>{
                    handleKeyUp(event)
                }}/>
                <div className="delete-button" onClick={handleDeleteButtonClick} />
            </InputContainer>
            {options.length && hasText ? (
                <DropDown
                options={options
                handleComboBox={handleDropDownClick}
                selectedOption={selectedOption}
                }
            />) : null}
        </div>
    )
}

export const DropDown = ({options, handleComboBox, selectedOption})=>{
    return (
        <DropDownContainer>
            {options.map(option,index)=>{
                return (
                    <li
                        key={index}
                        onClick={()=>handleComboBox(option)}
                        className={selectedOption === index ? "select" : ""}
                    >
                        {option}
                    </li>
                )
            }}
        </DropDownContainer>
    )
}
```

### stories 작성

- 컴포넌트명과 `stories.js` 를 작성하면 컴포넌트의 `stories`로 인식한다.
- Tag 컴포넌트를 불러온다.
- 기본값의 `title`은 `Example`의 폴더 안에 Tag 컴포넌트를 의미한다.
- 기본값의 `component`는 Tag 컴포넌트를 지칭한다.
- `stories`는 storybook
- [stories 설명보기](https://choigirang.github.io/posts/3-React-Storybook/){:target="\_blank"}

```jsx
import React from "react";
import { Tag } from "./components/Tag.js";

export default {
  title: "Example/Tag",
  component: Tag,
};

const Template = (args) => <Tag {...args} />;

export const Primary = Template.bind({});
Primary.args = {
  primary: true,
  label: "Tag",
};
```

### stories 작성

- 컴포넌트명과 `stories.js` 를 작성하면 컴포넌트의 `stories`로 인식한다.
- Autocomplete 컴포넌트를 불러온다.
- 기본값의 `title`은 `Example`의 폴더 안에 Autocomplete 컴포넌트를 의미한다.
- 기본값의 `component`는 Autocomplete 컴포넌트를 지칭한다.
- `stories`는 storybook
- [stories 설명보기](https://choigirang.github.io/posts/3-React-Storybook/){:target="\_blank"}

```jsx
import React from "react";
import { Autocomplete } from "./components/Autocomplete.js";

export default {
  title: "Example/Autocomplete",
  component: Autocomplete,
};

const Template = (args) => <Autocomplete {...args} />;

export const Primary = Template.bind({});
Primary.args = {
  primary: true,
  label: "Autocomplete",
};
```
