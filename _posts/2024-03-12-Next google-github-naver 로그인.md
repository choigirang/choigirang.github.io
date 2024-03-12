---
layout: post
title: Next 23장 - 소셜 로그인 (3) google, naver, github
author: admin
date: 2024-03-12 00:00:00 +900
lastmod: 2024-03-12 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [NEXT]
tags: [next, react, kakao, social, login, token]
---

# Next

## next-auth

### Google

[구글 Cloud](https://console.cloud.google.com/welcome)

<br>

[참고자료](https://velog.io/@uni/NextAuth.js-%EA%B5%AC%EA%B8%80-%EB%A1%9C%EA%B7%B8%EC%9D%B8-Next%EB%B2%84%EC%A0%84-13.4.2)

<br>

1. 상단 로고 옆 리스트 선택 > 새 프로젝트 생성
2. 프로젝트를 만들고 좌측 메뉴바에서 사용자 인증 정보의 동의 화면 구성 클릭
3. 외부 버튼 클릭 후 동의 화면에서 앱 이름과 메일 주소 등의 정보 입력하고 다음 버튼을 눌러 저장한다.(앱 도메인 등의 작성란은 빈칸)
4. 동의 화면 생성 후 사용자 인증 정보 클릭 > 상단의 사용자 인증 정보 만들기
5. API 키 생성 > 마찬가지로 OAuth 클라이언트 ID 생성
6. 승인된 자바스크립트 원본에는 현재 사용하고 있는 도메인 (http://localhost:3000)을 입력하고, **승인된 리디렉션에는 http://현재 도메인/api/auth/callback/google** 을 입력한다.
   - 카카오와 마찬가지로 어디로 리디렉션할지
7. OAuth로 생성한 클라이언트 ID와 클라이언트 비밀번호가 SECRET으로 들어가게 된다.

```jsx
export default NextAuth({
  providers: [
    KakaoProvider({
      clientId: process.env.KAKAO_CLIENT_ID!,
      clientSecret: process.env.KAKAO_CLIENT_SECRET!,
    }),
    GoogleProvider({
      clientId: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
    }),
  ],
  pages: {
    signIn: "/",
  },
});
```

### Github

[깃허브 애플리케이션](https://github.com/settings/applications/new)

<br>

1. 마찬가지로 애플리케이션의 사용 중인 도메인과 **http://현재 도메인/api/auth/callback/github** 작성
2. 생성된 ID, SECRET 으로 **Provider** 제공
