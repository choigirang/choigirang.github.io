---
layout: post
title: Next 16장 - styled-components error (2)
author: admin
date: 2023-09-08 00:00:00 +900
lastmod: 2024-03-05 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [NEXT]
tags: [next, react, styled-components]
---

# Next

## 2024-03-05

- Next.js는 초기 SSR 방식으로 동작하며, 이후 사용자 측에서 CSR 방식으로 혼합되어 동작할 수 있다.
- 이러한 특성 때문에 페이지를 요청할 때, **해당 페이지를 렌더링하고 HTML을 클라이언트에게 전달하는데 이때 CSS가 포함한 스타일이 적용된 HTML이 생성된다.**
- 서버에서 렌더링된 HTML에는 일반적으로 미리 정의된 클래스명이 포함되지만, 클래스명은 서버에서 생성되기 떄문에 클라이언트에서 자바스크립트가 실행되기 전까지는 상호작용 하지 않는다.
- 이후 **클라이언트 측 렌더링에서는 자바스크립트가 브라우저에서 실행되고 페이지의 동적인 부분이 생성되며, 이때 자바스크립트가 클래스명을 추가하거나 제거할 수 있게 된다.**
- **스타일 컴포넌트와 같은 CSS-in-JS 라이브러리를 사용하는 경우, 자바스크립트가 브라우저에서 실행될 때 클래스 이름이 동적으로 생성되고 서버 사이드 렌더링에서 생성된 클래스 이름과 다른 것이다.**

## styled-components

- Next는 서버에서 실행되는 SSR(Server Side Rendering) 방식을 사용하고 있기 때문에 첫 페이지가 로드될 때, 클라이언트에서 생성된 CSR 컴포넌트의 클래스명이 일치하지 않는 경우가 발생한다.
- 그렇기에 위와 같은 에러가 발생할 수 있는데, 이는 `babel-plugin` 을 사용하여 환경에 따라 달라지는 `className`을 동일하게 만들어 준다.
- `npm install -D babel-plugin-styled-components`
- 설치 후 `.babelrc`라는 파일을 생성한다.

```json
// .babelrc
{
  "presets": ["next/babel"],
  "plugins": [
    [
      "babel-plugin-styled-components",
      {
        "ssr": true, // SSR을 위한 설정
        "pure": true // dead code elimination (사용되지 않는 속성 제거)
      }
    ]
  ]
}
```

- 이러한 설정 후에도 에러가 수정되지 않았기에 더 찾아보니 Next.js 12버전 이상부터는 swc를 사용하도록 권장하고 있다.
- [swc에 관한 블로그](https://fe-developers.kakaoent.com/2022/220217-learn-babel-terser-swc/)
- 이제 next.config.js 파일을 수정하여 설정해주도록 한다.

```js
/** @type {import('next').NextConfig} */
const nextConfig = {
  compiler: {
    styledComponents: true,
  },
};

module.exports = nextConfig;
```

- 주의 사항은 babel이 그대로 있는 경우, babel이 우선적으로 사용되기 때문에 플러그인을 밀고 swc를 사용해야 한다.

[또 다른 useMediaQuery를 사용할 때 발생하는 에러](https://tesseractjh.tistory.com/164)
