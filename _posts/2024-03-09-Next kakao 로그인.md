---
layout: post
title: Next 21장 - 소셜 로그인 (1) kakao
author: admin
date: 2024-03-09 00:00:00 +900
lastmod: 2024-03-09 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [NEXT]
tags: [next, react, kakao, social, login, token]
---

# Next

- 가입된 소셜 계정에 따라 로그인을 간편하게 할 수 있는 소셜 로그인 구현
- 본 포스팅은 프론트 단에서만 이뤄진 요청, 서버까지 구현 시, 카카오에서 사용자에게 전송된 인증 코드를 갖고, 서버에서 카카오톡으로 유효한 인증 요청 등의 과정을 거친다.

## Kakao API

[카카오 개발](https://developers.kakao.com/console/app)

- 카카오에서는 네비, 검색, 지도 등의 다양한 API 문서를 제공하고 있고 그 중 카카오 로그인을 사용해보겠다.

### 요약

[카카오 사용자 로그인](https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api)

1. 사이트 접속 후 네비게이션에서 **내 애플리케이션** 페이지로 이동한다.
2. 애플리케이션을 생성 후, **요약 정보** 에서 각각의 프로젝트에 맞는 앱 키를 사용하면 된다.
3. 이 후, **앱 권한 신청** 에서 프로젝트에 맞는 동의 항목을 설정한다.
4. **제품 설정** > **카카오 로그인**, 하단의 `Redirect URI`를 입력한다.
   - 사용자가 계정 사용 동의를 마치고 나면 설정한 `URI`로 이동되는데, 이 때 주소에 **code params**가 생성되어 이동된다.
   - 사용자 계정 동의 완료 > `http://사용자가 설정한 Redirect URI`**?code=토큰** 이와 같은 형태의 페이지로 이동된다.
5. **code**에 작성된 토큰을 다시 서버에 전송하여 사용자의 인증 정보가 허용된 `access_token`을 돌려받고, 이 토큰을 다시 서버에 전송하여 사용자의 정보를 돌려받는다.

### 사용법

#### 사용자 인증 코드

- 4번의 설명처럼 우선 카카오 서버에 코드를 `uri`로 돌려받는다.
- [카카오 사용자 로그인](https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api)을 보면 알 수 있듯이, 여기에는 개발자 애플리케이션에 나와있는 **앱 키**와 **Redirect URI** 가 필요하다.

```jsx
// .env
...APP_KEY = 사용자의 환경에 맞는 카카오 앱 키
...REDIRECT_URI = 사용자가 입력한 Redirect uri

// example components
const APP_KEY = process.env.APP_KEY
const REDIRECT = process.env.REDIRECT_URI

export default function Example(){

  const kakaoLogin = () => {
    window.location.href = `https://kauth.kakao.com/oauth/authorize?response_type=code&client_id=${APP_KEY}&redirect_uri=${REDIRECT}`
  }

  return (
    <button onClick={() => kakaoLogin()}>카카오 로그인</button>
  )
}
```

#### access_token

1. 위에서 앱 키와 리다이렉트 주소를 올바르게 입력했다면, 카카오 사용자 동의 페이지로 이동되고, 동의가 완료되면 리다이렉트 주소를 가진 페이지로 다시 이동된다.
2. `uri`에 담긴 **code**를 활용하여 다시 `access_token`을 요청한다.
3. **window.location**에서 `useEffect`를 활용하여 `code`가 있을 경우 `access_token`을 요청하는 로직으로 작성 시에는 **KOE302 에러** 가 발생하게 될 것이다.
4. 이 에러는 발급된 인증 코드를 재활용 했을 때 발생하는 에러로, 카카오는 매 요청마다 새로운 코드를 발급하기 때문에 항상 최신화가 이루어져야 한다.
5. **인증 코드를 가진 상태에서 또 인증 코드를 요청할 때, 카카오에서는 최신화를 유지하기 위해 에러를 내보낸다.**
   - 다른 페이지로 이동하여 사용자 로그인을 처리하고 다음 페이지로 이동시키면 된다.
6. 인증 코드를 갖고 이동된 `url`에서 **code params** 의 값을 찾아 `access_token`을 요청한다.

```jsx
// my page
// url = http://localhost:3000/my
export default function Example(){
  const kakaoLogin = () => {
    window.location.href = `https://kauth.kakao.com/oauth/authorize?response_type=code&client_id=${APP_KEY}&redirect_uri=${REDIRECT}`
  }

  return (
    <button onClick={() => kakaoLogin()}>카카오 로그인</button>
  )
}

// redirect로 이동된 페이지
// url => http://localhost:3000/oauth?code=...
export default function Example(){
  useEffect(() => {
    const code = new URL(document.location.toString()).searchParams.get("code");

    axios
      .post(`https://kauth.kakao.com/oauth/token?grant_type=authorization_code=${code}&client_id=${APP_KEY}&redirect)uri=${REDIRECT}`,
        {},
        {
          headers: {
            "Content-type": "application/x-www-form-urlencoded;charset=utf-8",
          },
        })
      .then(res => res.data)
  },[])

  return (...)
}
```

#### access_token을 활용한 사용자 정보

- POST 요청을 통해 돌려받은 데이터 안에는 `access_token`이 들어있다.
- 같은 방식으로 사용자 정보를 요청한다.
- 헤더의 인증 정보에 `Bearer`**access_token**을 헤더에 담아 요청하면 된다.
- 그러면 `data` 안에 카카오 계정의 아이디, 이름, 프로필 등의 정보가 담겨온다.
- 이를 활용하여 `loaclstorage`,`redux` 등에 담아서 사용하면 된다.

```jsx
.then(res => {
  const {access_token} = res.data;
  axios.post(
    "https://kapi.kakao.com/v2/user/me",
            {},
            {
              headers: {
                Authorization: `Bearer ${access_token}`,
                "Content-type":
                  "application/x-www-form-urlencoded;charset=utf-8",
              },
            }
  ).then(res => {
    const {id, properties } = res.data;
  })
})
```

### next auth

- 이 모든 과정을 좀 더 수월하게 처리할 `next auth` 라이브러리를 사용해본다.
