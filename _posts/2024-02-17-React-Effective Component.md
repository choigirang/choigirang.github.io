---
layout: post
title: React 69장 - Effective Component 지속 가능 컴포넌트
author: admin
date: 2024-02-17 00:00:00 +900
lastmod: 2024-02-17  00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, refactor]
---

# React

[참고자료](https://toss.im/slash-22/sessions/1-7)

## Effective Component

- Effective Component란, 지속 성장 가능한 컴포넌트를 말한다.
- 쉽게 말해 변경에 용이하고 분리가 잘 되어 있는 컴포넌트로 만들어 기능을 수정하거나 추가할 때, 다른 컴포넌트에 영향을 주고 받지 않아 독립성이 유지되는 컴포넌트를 말한다.
- 우선 컴포넌트는 데이터를 관리하고 이를 사용자에게 보여주는 역할을 한다.
- 데이터는 `useState`나 전달받은 `props`를 컴포넌트 내에서 다루고 이를 UI로 펼쳐 유저에게 보여주는 역할을 하는데, 하나의 모듈화 된 컴포넌트에서는 데이터를 다루고 이를 UI로 보여주는 역할을 하는 컴포넌트에서 사용하여 각 각 독립성을 유지하는 컴포넌트를 만드는 것이 중요하다.

### Headless 컴포넌트 - 동작 추상화

```jsx
function Calendar(){
    const {headers, body, view} = useCalendar();

    return (
        <Table>
            <THead>
                {headers.weekDays.map(({key, value})) =>
                    <Th key={key}>{format(value, "E", {locale})}</Th>
                }
            </THead>
            <TBody>
                {body.value.map(({key, value:day}) => <Td key={key}>{getData(day)}</Td>)}
            </TBody>
        </Table>
    )
}
```

- 앞서 말했듯 디자인을 제외하고 데이터에만 집중화된 컴포넌트 모듈화를 Headless 컴포넌트라고 칭한다.
- 스타일이 없고 로직만 존재하는 것을 뜻하며, 이렇게 작성된 컴포넌트는 스타일 수정이 자유롭기 때문에 기능 변경이 많은 곳에 유용하다.
- 예시 코드를 살펴보면, 캘린더를 보여줄 컴포넌트가 있다.
- 이 캘린더 안에서는 `useCalendar`라는 훅을 통해 데이터를 전달받고 있다.
- `Calendar` 컴포넌트는 `useCalendar` 훅을 사용하여 데이터를 전달받고 있기 때문에, 캘린더의 역할을 할 데이터를 훅에 위임했다고 할 수 있다.
- 따라서, 또 다른 달력이 필요하다면 `useCalendar` 훅을 사용할 수 있다.
- 위의 예시처럼, 데이터에만 집중하여 모듈화하고, 한 가지 문제에만 집중하기 때문에 데이터와 UI 시스템이 분리되어 있다고 할 수 있다.

```jsx
function BtnPress(){
    return (
        <Btn
            onKeyDown={e => {...}}
            onKeyUp={e => {...}}
            onMouseDown={e => {...}}
            onMouseUp={e => {...}}
        />
    )
}

// Headless
function useBtnPress(){
    return {
        onKeyDown={e => {...}}
        onKeyUp={e => {...}}
        onMouseDown={e => {...}}
        onMouseUp={e => {...}}
    }
}

function BtnPress(){
    const pressProps = useBtnPress();

    return (
        <Btn {...pressProps}/>
    )
}
```

- 버튼을 눌러서 어떠한 기능을 하는 `BtnPress` 컴포넌트가 있다.
- 버튼에 대한 기능 동작을 데이터 추상화를 통해 `useBtnPress` 훅으로 분리한다.
- 훅에서 반환되는 데이터와 디자인 시스템을 분리하였기에 어떻게 보여질지에만 집중하면 된다.
- `Btn` 컴포넌트 뿐만 아니라, 다른 컴포넌트에서 위와 같은 동작을 사용한다고 하더라도 훅을 사용하면 되기 때문에 쉽게 사용이 가능하다.

### 합성 가능한 컴포넌트 만들기

```jsx
function ReactFrameworkSelect({ defaultValue }) {
  const [isOpen, open, close] = useBoolean();
  const [selected, change] = useState(defaultValue);

  return (
    <ReactFragment>
      <InputBtn label="React Framework" value={selected} onClick={open} />
      {isOpen && (
        <Options>
          {options.map((value) => (
            <Btn selected={selected === value} onClick={() => change(value)}>
              {value}
            </Btn>
          ))}
        </Options>
      )}
    </ReactFragment>
  );
}
```

- 프레임 워크를 선택하는 `select`가 있다.
- 프레임 워크를 선택하는 하나의 컴포넌트에서 클릭했을 때의 모달창, 펼쳐진 모달창에서 선택하는 아이템 일치 여부, 열고 닫히는 상태에 따른 노출 여부 등의 여러가지 데이터를 다루기 때문에 구조가 변경됐을 때에 재사용하기가 매우 어려워진다.
- 이렇게 유연하지 못한 컴포넌트를 독립적인 컴포넌트로 분리하여 생각해보면 된다.
  1. 메뉴의 노출 여부를 제어하는 `isOpen` => `Dropdown`
  2. 상태를 바꾸기 위해 사용작용하는 `Trigger` => `Dropdown.Trigger`
  3. 열고 닫히는 상태에 따른 노출 여부인 `Menu` => `Dropdown.Menu`
  4. 메뉴를 구성하는 각각의 `Item` => `Dropdown.Item`
- 이렇게 담당하고 있는 역할을 기준으로 분리해본다.

```jsx
function FrameworkSelect () {
    const {data: {frameworks} } = useFrameworks() ;
    const [selected, change] = useState() ;

    return (
        <Select trigger={...컴포넌트} value={selected} onChange={change} options={frameworks}/›
    )
}
```

```jsx
function Select({ label, trigger, value, onChange, options }: Props) {
  return (
    <Dropdown label={label} value={value} onChange={onChange}>
      <Dropdown.Trigger as={trigger} />
      <Dropdown.Menu>
        {options.map((option) => (
          <Dropdown.Item> {option}</Dropdown.Item>
        ))}
      </Dropdown.Menu>
    </Dropdown>
  );
}
```

- 담당하고 있는 역할을 기준으로 컴포넌트를 분리했다.
- `Dropdown` 은 내부적으로 `Menu`가 보일지 말지를 결정하고 `Trigger`와 연결된다.
- `Item`에서 발생한 `onClick` 이벤트는 `Dropdown`의 `onChange`와 이어진다.
- 맨 처음 작성된 `label`로 전달되는 `...컴포넌트`는 기존의 컴포넌트에서 분리되어 `Select` 컴포넌트와 `trigger`로 들어갈 컴포넌트는 서로의 존재를 알지 못한다.
- `trigger`로 전달되는 컴포넌트의 변경은 `Select` 컴포넌트 자체에는 아무런 변화를 주지 않기 때문에 서로에게 영향을 끼치지 않고, 컴포넌트의 변경에 더욱 유연해졌다.

### 정리

- 컴포넌트의 역할에 대한 분리를 통해 변화에 유연하게 대응할 수 있는 컴포넌트를 만들자.
- 하나의 컴포넌트 안에서는 역할에 맞게 디자인과 데이터에만 집중할 수 있도록 모듈화하자.
- 만들기 전에 인터페이스를 먼저 고민하고 내가 사용하듯이 생각하여 각 컴포넌트를 분리하여 만들자.
- 인터페이스를 고민하며, 컴포넌트의 의도가 무엇인지, 기능은 무엇인지, 어떻게 표현돼야 하는지를 먼저 생각해보자.
- 또한 무작정 컴포넌트를 분리하는 것이 아니라 **실제로 복잡도를 낮출 수 있는가**에 대해서도 충분히 고민해보자.
