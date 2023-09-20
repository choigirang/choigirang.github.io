---
layout: post
title: Backend 3장 - JWT
author: admin
date: 2023-08-08 00:00:00 +900
lastmod: 2023-08-08 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [BACKEND]
tags: [jwt, token]
---

# Backend

## JWT

- `JSON WEB TOKEN`의 줄임말로, 토큰을 생성하고 검증하는 데 사용되는 라이브러리다.
- `jwt.sign()`을 호출하고, `sign`은 문자열을 반환하며 이 문자열이 `token`이 된다.
- 호출된 `sign`에는 인자와 입력값을 전달하는데, 토큰에 인코딩하려는 데이터를 말하며 문자열, 객체, 버퍼 중 하나가 된다.
  - `jwt.sign({}) // 객체`

```js
// jwt.sign()
let token;
token = jwt.sign({userId: createdUser.id, email: createdUser.email}, "supersecret", {expiresIn: "1h", ...});
```

- `createdUser`로 DB에 저장한 유저 정보의 `id, email`을 가지고 키를 만든다.
- 두 번째 인자로는 프라이빗 키를 지정하는데, 이는 클라이언트와 공유되지 않으며, 세 번째 인자로는 선택사항으로 토큰을 구성한다.
  - 프라이빗 키를 모르면 위조나 편집을 시도하더라도 무효한 토큰이 된다.
  - 사용자 ID를 수정해서 토큰을 다시 생성하더라도 프라이빗 키를 모른다면 토큰은 복제할 수 없다.
- 세 번째 인자에는 여러 가지 옵션이 들어갈 수 있으며, 위에서는 `expiresIn`를 이용하여 토큰 만료 기한을 설정한다.
  - 공식 문서에서 사용할 수 있는 시간 문자열과 구성 옵션에 대한 모든 정보를 찾아볼 수 있다.
  - 토큰에 만료 시간을 설정하는 것이 권장된다.
