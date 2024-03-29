---
layout: post
title: Backend 1장 -  Token
author: admin
date: 2023-03-08 00:00:00 +900
lastmod: 2023-03-08 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [BACKEND]
tags: [backend, server, token, hashing, salt]
---

# Backend

## Hashing

- 가장 많이 쓰이는 암호화 방식 중 하나로, 복호화가 가능한 다른 암호화 방식들과 달리, **해싱은 암호화만** 가능하다.
- 항상 같은 길이의 문자열을 리턴하고, 서로 다른 문자열에 해시 함수를 사용하면 반드시 다른 결괏값이 나온다.
- 동일한 문자열에 동일한 함수를 사용하면 항상 같은 결괏값이 나온다.

### 레인보우 테이블과 솔트

![image](https://user-images.githubusercontent.com/118104644/223588159-8a64afc0-80e8-4c35-9403-a922d7cebb95.png){:.center}

- 함수를 거치기 이전의 값을 알아낼 수 있도록 기록해놓은 표인 레인보우 테이블이 존재한다.
- 기록된 값이 유출이 되었을 때 해싱을 했더라도 해싱 이전의 값을 알아낼 수 있으므로 보안상 위협이 될 수 있다.
- 이 때 사용하는 것이 **솔트**이며, 소금을 치듯 해싱 이전 값에 임의의 값을 더해 데이터가 유출 되더라도 해싱 이전의 값을 알아내기 어렵게 만드는 방법이다.
- 솔트를 사용하면 해싱 값이 유출되더라도, 솔트가 함께 유출 된 것이 아니라면 암호화 이전의 값을 알아내는 것은 불가능에 가깝다.

### 해싱의 목적

- 해싱의 목적은 데이터 그 자체를 사용하는 것이 아니라 동일한 값의 데이터를 사용하고 있는지 여부만 확인한다.
- 복호화가 불가능하도록 저장하여 데이터 유출 위험성을 줄이며 데이터의 유효성을 검증하기 위해 사용하는 단방향 암호화 방식이다.

## Token

### 토큰 인증 방식과 등장 배경

- **세션 기반 인증이 가지고 있는 한계를 극복하고자 고안되었다.**
- 세션 기반 인증은 서버에서 유저의 상태를 관리하고, 서버의 부담을 줄이기 위해 서버가 사용자의 인증 상태를 저장하는 것이 아닌 클라이언트에 이를 저장하는 방식인 **토큰 기반 인증 방식**이 등작했다.
- **유저의 인증 상태를 클라이언트에 저장할 수 있는 방식이다.**
- 토큰은 인증과 권한 정보를 담고 있는 암호화된 문자열을 말한다.

### 토근 인증 방식의 흐름

![image](https://user-images.githubusercontent.com/118104644/223592971-19489d11-54f6-4dea-b0e5-acb9c7bb8260.png){:.center}

1. 사용자가 인증 정보를 담아 서버에 로그인 요청을 보낸다.
2. 서버는 데이터베이스에 저장된 사용자의 인증 정보를 확인한다.
3. 인증에 성공했다면 해당 사용자의 인증 및 권한 정보를 서버의 비밀 키와 함께 토큰으로 암호화한다.
4. 생성된 토큰을 클라이언트로 전달한다.
   1. HTTP 상에서 인증 토큰을 보내기 위해 사용하는 헤더인 Authorization 헤더를 사용하거나, 쿠키로 전달하는 등의 방법을 사용한다.
5. 클라이언트는 전달받은 토큰을 저장한다.
   1. 저장하는 위치는 Local Storage, Session Storage, Cookie 등 다양하다.
6. 클라이언트가 서버로 리소스를 요청할 때 토큰을 함께 전달한다.
   1. 토큰을 보낼 때에도 Authorization 헤더를 사용하거나 쿠키로 전달할 수 있다.
7. 서버는 전달받은 토큰을 서버의 비밀 키를 통해 검증하며, 이를 통해 토큰이 위조되었는지 혹은 토큰의 유효 기간이 지나지 않았는지 등을 확인한다.
8. 토큰이 유효하다면 클라이언트의 요청에 대한 응답 데이터를 전송한다.

### 토큰 인증 방식의 장점

- **무상태성**
  - 서버가 유저의 인증 상태를 관리하지 않는다.
  - 서버는 비밀 키를 통해 클라이언트에서 보낸 토큰의 유효성만 검증한다.
- **확장성**
  - 다수의 서버가 공통된 세션 데이터를 가질 필요가 없다.
  - 이를 통해 서버 확장이 매우 용이하다.
- **어디서나 토큰 생성 가능**
  - 토큰의 생성과 검증이 하나의 서버에서 이루어지지 않기 때문에 토큰 생성만을 담당하는 서버를 구축할 수 있다.
  - 여러 서비스 간의 공통된 인증 서버를 구현할 수 있다.
- **권한 부여에 용이**
  - 인증 상태, 접근 권한 등 다양한 정보를 담을 수 있기 때문에 사용자 권한 부영 용이하다.
  - 어드민 권한 부여 및 정보에 대한 접근 범위 설정도 가능하다.

### JWT (JSON Web Token)

- 토큰 인증 구현 시 대표적으로 사용하는 기술이다.
- **JSON** 객체에 정보를 담고 이를 토큰으로 암호화하여 전송한다.
- 클라이언트가 서버에 요청을 보낼 때, 인증정보를 암호화된 JWT 토큰으로 제공하고, 이 토큰을 검증하여 인증정보를 확인할 수 있다.

#### JWT 구성

![image](https://user-images.githubusercontent.com/118104644/223594036-844dc9ad-744a-41a0-aa8f-1341ab00ce36.png){:.center}

- **Header**

  ```js
  {
    "alg" : "HS256",
    "typ" : "JWT",
  }
  ```

  - HTTP 헤더처럼 해당 토큰 자체를 설명하는 데이터가 담겨져 있다.
  - 토큰의 종류, 시그니처를 만들 때 사용할 알고리즘을 JSON 형태로 작성한다.
  - 이 JSON 객체를 base64 방식으로 인코딩하면 JWT의 첫 번째 부분인 Header가 완성된다.
    - base64 방식은 원한다면 얼마든지 디코딩할 수 있는 인코딩 방식이다.
    - 비밀번호와 같이 노출되어서는 안 되는 민감한 정보를 담지 않도록 해야 한다.

- **Payload**

  ```js
  {
    "sub": "someInformation",
    "name": "phillip",
    "iat": 151623391
  }
  ```

  - HTTP 페이로드와 마찬가지로 전달하려는 내용물을 담고 있다.
  - 어떤 정보에 접근 가능한지에 대한 권한, 유저의 이름과 같은 개인정보, 토큰의 발급 시간 및 만료 시간 등의 정보들을 JSON 형태로 담는다.
  - 이 JSON 객체를 base64 방식으로 인코딩하면 JWT의 두 번째 부분인 Payload가 완성된다.

- **Signature**
  ```js
  HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret);
  ```
  - 토큰의 무결성을 확인할 수 있는 부분이다.
  - Header와 Payload가 완성되었다면 Signature는 이를 서버의 비밀 키(암호화에 추가할 salt)와 Header에서 지정한 알고리즘을 사용하여 해싱한다.
  - 만약 HMAC SHA256 알고리즘을 사용한다면 Signature는 위와 같은 방식으로 생성된다.
  - 누군가 권한을 속이기 위해 토큰의 Payload를 변조하는 등의 시도를 하더라도 토큰을 발급할 때 사용한 Secret을 정확하게 알고 있지 못한다면 유효한 Signature를 만들어낼 수 없기 때문에 서버는 Signature를 검증하는 단계에서 올바르지 않은 토큰임을 알아낼 수 있다.

### 토큰 인증 방식의 한계

- Signature를 사용해서 위조된 토큰을 알아낼 수 있지만, 토큰 자체가 탈취된다면 토큰 인증 방식의 한계가 드러난다.

#### 무상태성

- 인증 상태를 관리하는 주체가 서버가 아니기 때문에, 토큰이 탈취되어도 해당 토큰을 강제로 만료시킬 수 없다.
- 토큰이 만료될 때까지 사용자로 가장해 계속해서 요청을 보낼 수 있다.

#### 유효 기간

- 토큰이 탈취되는 상황을 대비해 유효 기간을 짧게 설정하면, 토큰이 만료될 때마다 다시 로그인을 진행해야 하기 때문에 좋지 않은 사용자 경험을 제공한다.
- 유효 기간을 길게 설정하면 토큰이 탈취될 경우 치명적으로 작용한다.

#### 토큰의 크기

- 토큰에 여러 정보를 담을 수 있는 만큼, 많은 데이터를 담으면 그만큼 암호화하는 과정도 길어지고 토큰의 크기도 커지기 때문에 네트워크 비용 문제가 생길 수 있다.

### 액세스 토큰과 리프레시 토큰

- 토큰 인증의 한계를 극복하기 위한 대표적인 방법으로 액세스 토큰과 리프레시 토큰을 함께 사용한다.

#### Access Token

- **서버에 접근하기 위한 토큰**으로 보안을 위해 24시간 정도의 짧은 유효기간을 설정한다.

#### Refresh Token

- **액세스 토큰이 만료되었을 때 새로운 액세스 토큰을 발급하기 위해 사용되는 토큰이다.**
- 액세스 토큰보다 긴 유효기간을 설정한다.
