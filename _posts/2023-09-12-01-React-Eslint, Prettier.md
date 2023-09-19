---
layout: post
title: React 51장 - Eslint, Prettier, Typescript 사용하기
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

- 매번 작성되어 있는 Eslint, Prettier를 사용하면서 막상 내가 적용하려고 하니 아무것도 못 하고 있는 나를 위한 정리글
- 둘 다 코드 컨벤션을 잡아주는 녀석이라고 알고 있었는데, 그냥 두 개 다 사용하면 되는 건 줄 알았던 차이점도 알아보는 글
- eslint는 코드 퀄리티를 보장하도록 도와주고, prettier는 코드 스타일을 깔끔하게 통일되도록 도와준다는 설명에 대한 글

## ESlint

- 개발 중 특정 기능을 구현할 때, 그 기능을 구현하기 위한 많은 방식이 있다.
  - ex: 함수 : 화살표 함수, for문 : Array 내장 forEach문
- 이처럼 여러 방식의 코드 작성을 일관성 있는 방식으로 구현해주는 역할이다.

## Prettier

- 코드 구현 방식에 대한 것이 아닌 줄 바꿈, 공백, 들여 쓰기 등 에디터에서 텍스트가 일관되게 작성되도록 도와준다.

### Eslint 설정

`npm install eslint`

- eslint를 설치한다.
- 그리고 eslint extension 도 무조건적으로 설치해야 하는데, extension 설명란을 보면 extension과 library 둘 다 설치 및 세팅이 되어 있어야 한다고 나와있다.
- 설치가 끝났으면 `.eslintrc` 파일을 통해 규칙을 정해주어야 한다.

```json
{
  "root": true,
  "plugins": ["@typescript-eslint"],
  "extends": ["eslint:recommended", "plugin:@typescript-eslint/recommended"],
  "parser": "@typescript-eslint/parser",
  "rules": {
    "@typescript-eslint/strict-boolean-expressions": [
      2,
      {
        "allowString": false,
        "allowNumber": false
      }
    ]
  }
}
```

- **root**

  - default는 true인데, 이 값이 true가 아니면, eslintrc 파일을 찾을 때, 해당 프로젝트 디렉토리 뿐 아니라, 내 PC의 root 파일 시스템 root 디렉토리까지 eslint를 찾는다.

- **plugins**

  - 우선 plugin 종류는 여러 가지 있다.
  - **eslint-config-airbnb-base**: 에어비엔비 린트 플러그인
  - **eslint-config-next**: Next.js 전용 린트 플러그인
  - **eslint-plugin-react**: 리액트 전용 플러그인
  - **eslint-plugin-prettier**: 린트 위에 사용할 프리티어 플러그인
  - **eslint-config-prettier**: 요건 린트 설정과 중복되는 부분이 있으면 프리티어 룰에서 제외하는 플러그인
  - **@typescript-eslint/eslint-plugin**: 타입스크립트 전용 린트
  - 프로젝트에 필요로 한 각 플러그인은 npm이나 yarn을 통해서 설치하면 된다.
  - 만약 위에 @typescript-eslint/eslint-plugin을 쓸 거면, eslintrc 파일의 plugins 배열에 해당 모듈에서 제공하는 @typescript-eslint를 장착시키면 되고, 다른 모듈도 같이 쓸 거면 배열에 같이 추가하면 된다.

- **parser**

  - 각 코드 파일을 검사할 파서를 설정하는 부분이다. 기본 설정은 espree이고, 특정 @typescript-eslint/eslint-plugin처럼 특정 플러그인을 사용한다면 해당 플러그인에서 제공하는 parser를 장착하면 된다.

- **extends**

  - eslint rule 설정이 저장되어 있는 외부 file을 extends 하는 부분이다.
  - 위에 .eslintrc처럼 extends에 eslint:recommended, plugin:@typescript-eslint/recommended를 장착시켜주면, 사용하려는 해당 플러그인에서 기본적으로 제공하는 rule set이 적용된다.
  - 변경하고 싶은 부분이 있다면 rules 쪽에서 커스터마이징 하면 된다.

- **rules**
  - 이쪽은 직접 lint rule을 적용하는 부분이다.
  - extends로 자동으로 설정된 rules 중에, 특정 rule을 끄거나, erorr를 warning으로 나오도록 변경하는 등 설정을 바꿀 수 있다.

### Prettier 설정

`npm install prettier`

- prettier는 extension을 이용하는 방법과 package에 직접 설치하여 사용하는 방법이 있다.
- 첫 번째 방법이 간단하지만, 협업을 하기 위해선 두 번째 방법이 일반적으로 사용되기 때문에 두 번째 방법만 알아보자
- eslint를 사용하면 `eslint-config-prettier`를 설치해서 세팅해주라고 하고 있는데, eslint와 중복되는 규칙을 알아서 꺼주는 역할을 한다.
- extention에 관한 글을 읽다보면 어떻게 세팅해야 하는지 친절하게 알려주고 있다.

```json
// .eslintrc
{
  "extends": ["prettier"],
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": "error",
    "arrow-body-style": "off",
    "prefer-arrow-callback": "off"
  }
}
```

- `extends: ["prettier"]` : eslint-config-prettier를 실제로 활성화시켜서 중복되는 룰을 꺼준다.
- `plugins: ["prettier"]`: prettier 플러그인을 등록한다.
- `prettier/prettier: "error"`: eslint 내에서 prettier가 돌아갈 때, prettier 규칙에 맞지 않는 요소들을 error로 판단한다.
- `arrow-body-style: "off", prefer-arrow-callback: "off"`: eslint와 같이 사용하는 부분에 있어, 내부적인 이슈가 있어서, 임의로 두 설정을 꺼둬야 한다.

```json
// .eslintrc
{
  "extends": ["plugin:prettier/recommended"]
}
```

```json
{
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode"
}
```

- command + shift + p 로 나온 검색창에서 `Open Settings.json`을 선택하여 에러로 판단되는 부분을 rul에 맞게 수정해주는 설정을 켠다.
- 또한 save할 때, Extension으로 설정된 prettier 환경에 맞게 코드를 수정해주는 옵션도 켜준다.
