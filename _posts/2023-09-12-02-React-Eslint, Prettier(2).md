---
layout: post
title: React 52장 - Eslint 오류
author: admin
date: 2023-09-12 00:00:00 +900
lastmod: 2023-09-12 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, eslint, prettier, tsx]
---

# React

## Eslint

```js
PS C:\Users\choigirang\Desktop\code-container> npm install @typescript-eslint/eslint-plugin
npm WARN config global `--global`, `--local` are deprecated. Use `--location=global` instead.
npm ERR! code ERESOLVE
npm ERR! ERESOLVE could not resolve
npm ERR!
npm ERR! While resolving: code-container@0.1.0
npm ERR! Found: @typescript-eslint/eslint-plugin@5.62.0
npm ERR! node_modules/@typescript-eslint/eslint-plugin
npm ERR!   @typescript-eslint/eslint-plugin@"^5.5.0" from eslint-config-react-app@7.0.1
npm ERR!   node_modules/eslint-config-react-app
npm ERR!     eslint-config-react-app@"^7.0.0" from react-scripts@5.0.0
npm ERR!     node_modules/react-scripts
npm ERR!       react-scripts@"5.0.0" from the root project
npm ERR!   peerOptional @typescript-eslint/eslint-plugin@"^4.0.0 || ^5.0.0" from eslint-plugin-jest@25.7.0
npm ERR!   node_modules/eslint-plugin-jest
npm ERR!     eslint-plugin-jest@"^25.3.0" from eslint-config-react-app@7.0.1
npm ERR!     node_modules/eslint-config-react-app
npm ERR!       eslint-config-react-app@"^7.0.0" from react-scripts@5.0.0
npm ERR!       node_modules/react-scripts
npm ERR!         react-scripts@"5.0.0" from the root project
npm ERR!   1 more (the root project)
npm ERR!
npm ERR! Could not resolve dependency:
npm ERR! @typescript-eslint/eslint-plugin@"*" from the root project
npm ERR!
npm ERR! Conflicting peer dependency: @typescript-eslint/parser@6.6.0
npm ERR! node_modules/@typescript-eslint/parser
npm ERR!   peer @typescript-eslint/parser@"^6.0.0 || ^6.0.0-alpha" from @typescript-eslint/eslint-plugin@6.6.0
npm ERR!   node_modules/@typescript-eslint/eslint-plugin
npm ERR!     @typescript-eslint/eslint-plugin@"*" from the root project
npm ERR!
npm ERR! Fix the upstream dependency conflict, or retry
npm ERR! this command with --force, or --legacy-peer-deps
npm ERR! to accept an incorrect (and potentially broken) dependency resolution.
npm ERR!
npm ERR! See C:\Users\choigirang\AppData\Local\npm-cache\eresolve-report.txt for a full report.

npm ERR! A complete log of this run can be found in:
npm ERR!     C:\Users\choigirang\AppData\Local\npm-cache\_logs\2023-09-11T15_00_31_723Z-debug-0.log
```

- Eslint 세팅 중 발생한 에러
- `npm install eslint`로 여러 가지 질문을 통해 설치가 진행되는데, 이와 관련된 패키지나 플러그인이 설치된다.
- 다른 블로그 글들을 찾아봐도 이상없이 잘 진행되던데 나는 계속 에러가 발생했다.
- 패키지 자체에 작성 후 `npm install`도 해보고 다 밀어버린 다음에 새로 깔아보는데도 같은 오류가 발생했다.
- 관련 에러에 대한 자료가 잘 안 나오던 중 드디어 찾을 수 있었다.
- [참조링크](https://stackoverflow.com/questions/76828444/failed-to-install-typescript-eslint-eslint-plugin)

- 글에서도 볼 수 있듯이 package-json 파일에서 eslint의 plugin을 찾아보면 필요한 버전을 볼 수 있다.
- 설치에 필요한 패키지 버전들을 먼저 설치 후 plugin을 설치

**23.09.15**

- 참고하여 package.json에서 종속성을 하나하나 찾아가며 설치하려 했으나 어느순간 막히게 됐다.
- `eslint --init`을 사용했을 때 설치되는 플러그인이 많았기에 기본으로 설치되는 라이브러리를 손수 설치하려 했으나 계속 종속성 오류를 뱉어내며 플러그인 등의 버전 충돌이 일어났다.
- eslint 설치를 포기할까 고민하다가 일단 다 밀어봤다.
- 대표적인 충돌은 ``eslint-parse`가 6버전 이상으로 설치됐어야 했는데 애초에 6버전 이상으로의 설치를 시도하면 버전 충돌이 났기 때문에 하나하나 버전을 입력해서 설치해보았다.

1. 우선 eslint의 타입스크립트에서 지속적인 에러가 발생하기 떄문에, 타입이 아닌 eslint 라이브러리는 유지했다.
   1. `eslint eslint-config-react-app eslint-plugin-react` 유지
2. 그 다음 다른 어떤 라이브러리를 설치하려고 할 때마다 계속 문제가 되었던 `parser`를 설치해본다.
   1. `npm install --save-dev @typescript-eslint/parser@6`
   2. 설치 성공
3. 어쩐 일로 정상적으로 `6.7.0`버전이 정상적으로 설치된 건지 모르겠지만 `plugin`을 설치해본다.
   1. `npm install --save-dev @typescript-eslint/eslint-plugin@6`
   2. 설치 성공
4. `plugin`도 설치되었기 때문에 `eslint-config-standard-with-typescript` 설치해본다.
   1. `eslint --init`을 사용하면 .elsintrc.json 파일이 생성되는데 `extends`에 기본적으로 eslint-config-standard-with-typescript 패키지가 있다.
   2. 여기서 계속 에러가 발생하는데 급한 불은 꺼보았다.

**eslint에 대해서 근본적으로 알아보고 설치하여야겠다. 애초에 eslint와 관련된 패키지 중 필요한 것들을 골라서 사용하면 될듯 하기에 추후 알아보도록 하자.**
