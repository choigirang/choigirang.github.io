---
layout: post
title: React 57장 - Element 추가해보기
author: admin
date: 2023-10-05 00:00:00 +900
lastmod: 2023-10-07  00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, typescript, element, render, ts]
---

# React

## TypeScript

### 어질어질 DOM 건들이기

- 넣고 싶은 기능이 있었다.
- 마크다운으로 작성된 코드 중 실질적으로 작성된 코드(```)를 쉽게 복사, 붙여넣기 하고 싶었다.
- 앞서 [55장](https://choigirang.github.io/posts/03-React-ToastUi/)에서 만들었던 `Viewer`를 건들여보기로 했다.
- 내가 원하는 것은 렌더링 될 때, 코드가 작성된 부분을 유동적으로 인식하고, 바로 위에 아이콘을 생성하여 아이콘을 클릭하면 아래에 `<pre>`로 작성된 코드 부분을 복사하는 것이었다.

```jsx
export default function ToastViewer({ content }: { content: string }) {
  const viewerRef = (useRef < Viewer) | (null > null);

  useEffect(() => {
    addIconHandler();
  }, [content]);

  const addIconHandler = () => {};

  return (
    <Viewer
    // ...
    />
  );
}
```

- 컴포넌트가 렌더링 될 때, `useEffect`로 아이콘을 추가하는 핸들러를 작동시킨다.
- 핸들러에서 `pre`태그를 찾아서 아이콘을 추가할 수 있도록 작성한다.

```jsx
const addIconHandler = () => {
  const viewContainer = viewerRef.currentRef?.getRootElement();

  if (viewContainer) {
    const codeEls = viewCotnainer.querySelectorAll("pre");
    // [pre.lang-js, pre.example, ...]

    codeEls.forEach((el) => {
      const iconContainer = document.createElement("div");
      iconContainer.className = "icon-box";
      el.parentNode?.inserBefore(iconContainer, el);
      ReactDOM.render(<ExampleIcon />, iconContainer);
    });
  }
};
```

- 렌더링되고 나면 생성된 `Elements` 중 `pre` 태그를 가진 요소를 찾는다.
- 요소를 전부 찾은 유사 배열 형태이기 때문에 `forEach`를 사용하여 요소를 생성하고, 각 요소 앞에 아이콘을 담고 있는 `icon-box` 클래스의 `div` 요소를 추가해준다.
- 그리고 요소 안에 `import`한 리액트 아이콘을 렌더링 시킨다.

### 귀찮으니 좀 더 줄여보자

- 위와 같이 요소를 생성하고, 클래스를 정해주고 등의 과정을 줄이고 싶었다.

```jsx
codeEls.forEach((el) => {
  const copyIcon = (
    <div className="copy-icon">
      <BsFillClipboardCheckFill />
    </div>
  );

  ReactDOM.render(copyIcon, codeElement.previousSibling);
});
```

- 변수 안에 생성하고자 하는 요소를 담고 바로 렌더링 시켜주려고 했다.
- 여기서 타입에러가 발생했다.

```
이 호출과 일치하는 오버로드가 없습니다.
  마지막 오버로드에서 다음 오류가 발생했습니다.
    'Element' 형식의 인수는 'ReactElement<any, string | JSXElementConstructor<any>>[]' 형식의 매개 변수에 할당될 수 없습니다.
      'ReactElement<any, any>' 형식에 'ReactElement<any, string | JSXElementConstructor<any>>[]' 형식의 length, pop, push, concat 외 31개 속성이 없습니다.ts(2769)
index.d.ts(112, 5): 여기서 마지막 오버로드가 선언됩니다.
const copyIcon: JSX.Element
```

- 이런 타입에러였는데, `ReactDOM.render`는 `JSXElement`가 아닌 `HTMLElement`를 사용해야 한다는 에러였다.
- `JSX`문법이라고 해도 렌더링 된 후에는 일반적인 요소들과 똑같은데 왜 타입 에러가 발생하는지 이해할 수 없었다.
- 요소로 감싸주지 않아서 발생하는 에러인가 싶어 `React.Fragment`로 감싸주기도 해봤는데 불가능...
- 에러에 대한 정보가 적어, 일단 변수로 사용하지 않고 자바스크립트 문법으로 요소를 생성해서 사용하기로 했지만 더 찾아보고 해결해봐야겠다.
