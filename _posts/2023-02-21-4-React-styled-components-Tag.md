---
layout: post
title: React 15장 - Tag 입력
author: admin
date: 2023-02-21 00:00:00 +900
lastmod: 2023-02-21 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]  # 대문자로 작성
tags: [react, components, jsx, styled-components, tag]
---
# React
## Tag
- 값을 입력 후 `Enter` 키를 누르면 값이 태그 형태로 들어가는 기능 구현

### 컴포넌트 짜기
- 컴포넌트를 나눈다.
- 우리가 작성해야 하는 것은 태그를 감싸고 있는 박스 하나.
    - 그 안에 태그를 달아주는 것과 입력하는 것을 모두 작성했지만 각 컴포넌트로 쪼갤 수 있다.
  - `ul`안에 `li`가 있고, `li`안에 `span`요소를 넣어, 이를 입력하는 배열만큼 각 `li`와 `span`안에 값을 뿌려 출력한다.


```jsx
return (
    <TagContainer>
        <ul>
            <li>
                <span></span>
            </li>
        </ul>
        <input/>
    </TagContainer>
)
```


### Styled-Components
- TagContainer 컴포넌트를 정렬한다.
- `ul`,`li`,`span`,`input`의 `style`을 정해준다.
- TagesContainer 컴포넌트는 중앙에 위치하며, 그 안에 오는 요소들은 컴포넌트의 시작인 좌측부터 차곡차곡 쌓인다.
  - 만약 컴포넌트의 넓이값을 넘어가면 아래로 떨어뜨려 정렬한다.
- `li`는 `tags` 클래스를 갖고 있다.
  - 넓이값은 우리가 입력하는 텍스트를 기준으로 자동으로 지정한다.
  - `li`안에 있는 텍스트는 상화좌우 자동정렬한다.
  - `li`의 `list-style`을 없앤다.
- `span`은 `tags-close-icon` 클래스를 갖고 있다.
- `input`은 `flex` 비율 1값을 갖고 있다.
  - `input`요소를 클릭하여 `focus`되면 외곽선이 투명해진다.
  - `input`이 `focus-withon`이 되었을 때 외곽선이 생기는 것을 색상을 변경해준다.




```jsx
import {useState} from "react"
import styled from "styled-components"

export const TagContainer.div`
    margin: 8rem auto;
    display: flex;
    align-items: flex-start;
    flex-wrap: wrap;
    min-height: 48px;
    width: 480px;
    padding: 0 8px;
    border: 1px solid rgb(214, 216, 218);
    border-radius: 6px;

    > ul {
        display: flex;
        flex-wrap: wrap;
        padding: 0;
        margin: 8px 0 0;

        > .tag {
            width: auto;
            height: 32px;
            display: flex;
            align-items: center;
            justify-content: center;
            color: #fff;
            padding: 0 8px;
            font-size: 14px;
            list-style: none;
            border-radius: var(--coz-purple-600);
            > .tag-close-icon {
                display: block;
                width: 16px;
                height: 16px;
                line-height: 16px;
                text-align: center;
                font-size: 14px;
                margin-left: 8px;
                color: var(--coz-purbple-600);
                border-radius: 50%;
                background: #fff;
                cursor: pointer;
            }
        }
    }

    > input {
        flex: 1;
        border: none;
        height: 46px;
        font-size: 14px;
        padding: 4px 0 0 0;
        :focus {
            outline: transparent;
        }
    }

    &:focus-within {
        border: 1px solid var(--coz-purple-600);
    }
`
```


### 기능 구현
#### 상태값 작성
- 초기값은 배열 `["choi","kim"]`을 갖고 있다.
- 태그를 작성하고 `Enter`키를 눌렀을 때 함수를 실행한다.
  - 만약 기존값과 내가 이벤트가 발생한 `event.target`의 입력값인 `value`와 겹치는 값이 없다면,
  - 입력한 `value`값이 공백이 아니라면,
  - 기존 배열에 내가 작성한 `event.target.value`를 넣어준다.
- `close icon`이 부여된 `span`을 눌렀을 때에 함수를 실행시킨다.
  - 작성된 기존값의 `index`와 내가 클릭한 값의 `index`를 일치시켜, 일치하는 값을 제외한 배열을 재구성한다.


```jsx
const allTags = ["choi","kim"]

const [tags,setTags] = useState(allTags)

const removeTags = (indexToRemove) => {
    setTags(tags.filter((tag)=> tag !== tags[indexToRemove]))
}

const addTags = (event) => {
    if(
        event.key === "Enter" &&
        !tags.includes(event.target.value) &&
        event.target.value !== ""
    ){
        setTags([...tags, event.target.value]);
        event.target.value = "";
    }
}
```


#### 컴포넌트에 이벤트 핸들러 부여
- `li` 요소 안 `span`에 우리가 배열이 가진 값을 부여해 출력한다.
- `map()`의 전달인자`index`는 배열의 `index`를 말하며 `span className="tags-close-icon"`을 눌렀을 때, `removeTags`를 실행시킨다.
  - `removeTags`에 `map()`으로 넘겨받은 `index`를 `tag.filter`에 넘겨주어 내가 클릭한, 이미 작성된 값의 `index`를 `filter`로 거른 새로운 배열이 된다.



```jsx
return (
    <>
        <TagContaier>
            <ul>
                {tags.map((tag, index)=> {
                    <li key={index} className="tags">
                        <span className="tag-title">{tags}</span>
                        <span className="tags-close-icon" onClick={()=>removeTags(index)}>
                        </span>
                })}
            </ul>
            <input className="tag-input" type="text"
            onClick={(event)=>{
                addTags(event)
            }} placeholder="Press enter to add tags"><>
        </TagContaier>
    </>
)
```


#### 코드
```jsx
import {useState} from "react"
import styled from "styled-components"

export const TagContainer.div`
    margin: 8rem auto;
    display: flex;
    align-items: flex-start;
    flex-wrap: wrap;
    min-height: 48px;
    width: 480px;
    padding: 0 8px;
    border: 1px solid rgb(214, 216, 218);
    border-radius: 6px;

    > ul {
        display: flex;
        flex-wrap: wrap;
        padding: 0;
        margin: 8px 0 0;

        > .tag {
            width: auto;
            height: 32px;
            display: flex;
            align-items: center;
            justify-content: center;
            color: #fff;
            padding: 0 8px;
            font-size: 14px;
            list-style: none;
            border-radius: var(--coz-purple-600);
            > .tag-close-icon {
                display: block;
                width: 16px;
                height: 16px;
                line-height: 16px;
                text-align: center;
                font-size: 14px;
                margin-left: 8px;
                color: var(--coz-purbple-600);
                border-radius: 50%;
                background: #fff;
                cursor: pointer;
            }
        }
    }

    > input {
        flex: 1;
        border: none;
        height: 46px;
        font-size: 14px;
        padding: 4px 0 0 0;
        :focus {
            outline: transparent;
        }
    }

    &:focus-within {
        border: 1px solid var(--coz-purple-600);
    }
`
export const Tag = () => {
    const allTags = ["choi","kim"]

    const [tags,setTags] = useState(allTags)

    const removeTags = (indexToRemove) => {
        setTags(tags.filter((tag)=> tag !== tags[indexToRemove]))
    }

    const addTags = (event) => {
        if(
            event.key === "Enter" &&
            !tags.includes(event.target.value) &&
            event.target.value !== ""
        ){
            setTags([...tags, event.target.value]);
            event.target.value = "";
        }
    }

    return (
        <>
            <TagContaier>
                <ul>
                    {tags.map((tag, index)=> {
                        <li key={index} className="tags">
                            <span className="tag-title">{tags}</span>
                            <span className="tags-close-icon" onClick={()=>removeTags(index)}>
                            </span>
                    })}
                </ul>
                <input className="tag-input" type="text"
                onClick={(event)=>{
                    addTags(event)
                }} placeholder="Press enter to add tags" />
            </TagContaier>
        </>
    )
}
```

### stories 작성
- 컴포넌트명과 `stories.js` 를 작성하면 컴포넌트의 `stories`로 인식한다.
- Tag 컴포넌트를 불러온다.
- 기본값의 `title`은 `Example`의 폴더 안에 Tag 컴포넌트를 의미한다.
- 기본값의 `component`는 Tag 컴포넌트를 지칭한다.
- `stories`는 storybook
- [stories 설명보기](https://choigirang.github.io/react/3-React-Storybook/){:target="_blank"}


```jsx
import React from "react";
import {Tag} from "./components/Tag.js"

export default{
    title: "Example/Tag",
    component: Tag
};

const Template = (args) => <Tag {...args} />;

export const Primary = Template.bind({});
Primary.args = {
    primary: true,
    label: "Tag"
}
```