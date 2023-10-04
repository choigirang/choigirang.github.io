---
layout: post
title: React 54장 - Toast Editor (2) 커스텀과 저장하여 사용하기
author: admin
date: 2023-10-04 00:00:00 +900
lastmod: 2023-10-04  00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, editor, toast, typescript]
---

# React

## Toast Ui

- `toast ui` 공식 문서를 보면 여러가지 옵션값과 플러그인을 갖고 있는 것을 확인할 수 있다.
- [Github](https://github.com/nhn/tui.editor) | [Examples](https://ui.toast.com/tui-editor)
- 리액트 18버전과는 호환되지 않는다는 단점이 있지만, 여러가지 플러그인과 옵션을 설정할 수 있다는 것에 매우 큰 이점이 있다고 생각한다.
- 우선 기본적인 구조는 아래와 같다.

```jsx
import "@toast-ui/editor/dist/toastui-editor.css";
import "@toast-ui/editor/dist/theme/toastui-editor-dark.css";
import "prismjs/themes/prism.css";
import "@toast-ui/editor-plugin-code-syntax-highlight/dist/toastui-editor-plugin-code-syntax-highlight.css";

import Prism from "prismjs";
import codeSyntaxHighlight from "@toast-ui/editor-plugin-code-syntax-highlight";
import { Editor } from "@toast-ui/react-editor";

export default function ToastEditor() {
  return (
    <Editor
      height="100%"
      initialEditType="markdown"
      useCommandShortcut={true}
      theme="dark"
      previewStyle="tab"
      language="ko-KR"
      plugins={[[codeSyntaxHighlight, { highlighter: Prism }]]}
    />
  );
}
```

1. `plugins`과 `theme`는 개인의 선택에 맞게 사용하면 된다.
2. `previewStyle` : 좌측 상단에 `preview` 버튼에 관한 미리보기 설정이다.
   1. `vertical` : 우측에 미리보기를 볼 수 있다.
   2. `tab` : 좌측 상단에 `write, preview`가 생긴다.
3. `theme` : css를 불러와서 theme를 지정할 수 있다.
4. `codeSyntaxHighlight` : 공식 문서에 있는 플러그인을 설치하여, 코드로 작성된 하이라이트의 색상에 대한 옵션이다.
5. `initialValue` : 초깃값 설정으로 처음 에디터에 작성되있을 텍스트를 지정할 수 있다.
6. `initialEditType` : 에디터 작성 모드에 대한 설정이다. (markdown, wysiwyg)
7. `hideModeSwitch` : 에디터 작성 모드에 대한 탭을 숨길 수 있다.
8. `toolbarItems` : 툴바 옵션에 대한 설정이다.

```js
toolbarItems={[ // 툴바 옵션 설정
            ["heading", "bold", "italic", "strike"],
            ["hr", "quote"],
            ["ul", "ol", "task", "indent", "outdent"],
            ["table", /* "image", */ "link"],
            ["code", "codeblock"],
          ]}
```

9. 글자색을 변경하는 플러그인을 설치 후, 해당하는 옵션을 설정할 수 있지만, 나에겐 필요하지 않기 때문에 생략한다.

### source error

- 프로젝트 시작 시 `source map`에러가 발생할 수 있다.

```
Failed to parse source map from '~\node_modules\@toast-ui\editor\dist\purify.js.map'
file: Error: ENOENT: no such file or directory,
open 'D:\project\codiary\client\node_modules\@toast-ui\editor\dist\purify.js.map'
```

- package.json에 `source map` 에 대한 추가 설정을 해준다.

```json
"scripts": {
    "start": "set \"GENERATE_SOURCEMAP=false\" && react-scripts start",
    "build": "set \"GENERATE_SOURCEMAP=false\" && react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "predeploy": "npm run build",
    "deploy": "gh-pages -d build"
  },
```

### 저장과 초기화

- `useRef`를 사용하여 현재 에디터에 작성된 내용을 HTML이나 Markdown으로 가져올 수 있다.
  - 초깃값은 `null`로 지정하고 타입은 에디터를 지정할 것이기 떄문에 `Editor`를 작성해준다.

```tsx
import "@toast-ui/editor/dist/toastui-editor.css";
import "@toast-ui/editor/dist/theme/toastui-editor-dark.css";
import "prismjs/themes/prism.css";
import "@toast-ui/editor-plugin-code-syntax-highlight/dist/toastui-editor-plugin-code-syntax-highlight.css";

import Prism from "prismjs";
import codeSyntaxHighlight from "@toast-ui/editor-plugin-code-syntax-highlight";
import { Editor } from "@toast-ui/react-editor";

export default function ToastEditor() {
  const editorRef = useRef<Editor>(null);

  return (
    <Editor
      height="100%"
      ref={editorRef}
      initialEditType="markdown"
      useCommandShortcut={true}
      theme="dark"
      previewStyle="tab"
      language="ko-KR"
      plugins={[[codeSyntaxHighlight, { highlighter: Prism }]]}
    />
  );
}
```

- `ref`로 지정한 `Editor`에서 내용을 가져올 함수를 작성한다.
- `getInstance` 메서드를 사용하여 Toast Ui의 인스턴스를 가져올 수 있고, 현재 작성된 에디터의 내용을 가져올 수 있다.
- `setMarkdown, setOptions` 등을 통해 내용을 설정하거나 옵션을 바꾸는 것도 가능하다.

```js
const saveHandler = () => {
  let htmlContent = editorRef.current?.getInstance().getHTML() || "";
  let markdownContent = editorRef.current?.getInstance().getMarkdown() || "";
};
```

- 저장 버튼을 만들어 이벤트를 설정하면 현재 작성된 에디터의 내용을 확인할 수 있다.
