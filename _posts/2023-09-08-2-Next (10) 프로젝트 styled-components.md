---
layout: post
title: Next 16장 - styled-components error (2)
author: admin
date: 2023-09-08 00:00:00 +900
lastmod: 2023-09-08 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [NEXT]
tags: [next, react, styled-components]
---

# Next

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
