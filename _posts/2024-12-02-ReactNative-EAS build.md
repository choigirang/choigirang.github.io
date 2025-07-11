---
layout: post
title: React Native 1장 - EAS BUILD
author: admin
date: 2024-12-02 00:00:00 +900
lastmod: 2024-12-02 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [NATIVE]
tags: [ReactNative, Native, eas, expo]
---
# React Native
## EAS Build
- React Native 개발 진행
- EAS는 채널, 브랜치, 프로파일 등의 개념 존재
- build 관련 명령어
- ```eas build``` 시 production에 기본값으로 배포
- dev, preview 등의 설정을 따로 해야 production (store)가 아닌 개발 환경에서 확인 가능
- 브랜치, 프로파일 관련 명령어
- ```eas build --profile development(profile 기본값 production) --platform android```
- 플랫폼과 프로필 설정 명령어
- app.json, eas.json 에서 관련 세팅 가능

## app.json
```json
{
  "expo": {
    "name": "m-example",
    "slug": "smart-m-example",
    "runtimeVersion": "1.0.1",
    "version": "1.0.3",
    "orientation": "portrait",
    "icon": "./src/assets/images/icon.png",
    "scheme": "myapp",
    "userInterfaceStyle": "automatic",
    "splash": {
      "image": "./src/assets/images/splash.png",
      "resizeMode": "contain",
      "backgroundColor": "#ffffff"
    },
  "ios": {
        "supportsTablet": true,
        "bundleIdentifier": "com.expo.zap",
        "buildNumber": "19",
        "icon": "./src/assets/images/icon.png"
    },
    "android": {
        "adaptiveIcon": {
        "foregroundImage": "./src/assets/images/adaptive-icon.png",
        "backgroundColor": "#ffffff"
        },
        "icon": "./src/assets/images/icon.png",
        "package": "com.m.example",
        "versionCode": 19,
        "googleServicesFile": "./google-services.json"
        }
      },
  "updates": {
    "url": "https://u.expo.dev/example",
    "checkAutomatically": "ON_LOAD",
    "enabled": true,
    "fallbackToCacheTimeout": 0
  },
  "owner": "smart-example"
}
```
- runtimeVersion은 앱이 필요로 하는 최소 버전, 사용자에게 제공되는 어플의 version이 1.0.3이라면 가능 , 1.0.0 이면 runtimeVersion이 1.0.1 이기 때문에 동작하지 않음
- 파일 안에서 여러가지 세팅
- updates URL을 맞춰줘야 함

## eas.json
- 빌드에 관한 설정 파일

## updates
- ```eas update```, 현재 본인이 갖고 있는 채널 중 어디에 올릴 것인지 선택 가능
- eas는 js 변경 사항을 기준으로 자동 업데이트 되기 떄문에, 변경점이 존재하면 각 플랫폼의 versionCode, buildNumber를 하나씩 증가시켜줘야함
- 만약 js 가 아닌 npm install, change android file 등의 환경이 변경될 경우 version을 증가시키고 새롭게 빌드해야함

## EAS Build axios error
- 로컬에서는 잘 작동하던 서버가 build 후에 Network Error, Axios Error가 발생하는 경우
- cors 에러와 비슷하지만 서버에서 allow *와는 달리 react native에서 자체적으로 통신을 막는 현상

### try
1. `/android/app/src/main/AndroidManifest.xml`
  - `android:usesCleartextTraffic="true" android:networkSecurityConfig="@xml/network_security_config"` 추가
  - `/android/app/src/main/res/xml/network_security_config.xml`
```js
// AndroidManifest.xml
<application android:usesCleartextTraffic="true" android:networkSecurityConfig="@xml/network_security_config"></application>
```

```js
// network_security_config.xml 생성 후 추가
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config cleartextTrafficPermitted="true" />
</network-security-config>
```

2. app.json 에 플러그인 추가
    - `npx expo install expo-build-properties`
```js
"plugins": [
      [
        "expo-build-properties",
        {
          "android": {
            "usesCleartextTraffic": true
          }
        }
      ]
    ]
```

## EAS Build error
- free 버전을 사용하는 경우, 앱의 크기가 커지면서 or 다른 이유로 인해 build 시간이 지연되고 최종적으로는 fail이 뜨는 경우
```js
Build failed: We've lost connection to the worker.

Two common reasons:
1. Worker ran out of memory.
   Try running your build on a large worker. Learn more at https://docs.expo.dev/build/eas-json/#selecting-resource-class
2. Worker experienced a network issue.
   This is most likely an intermittent problem. Try running the build again.
```
- 이 경우 로컬에서 ```eas build --locale``` 시행이 된다면 eas 문제
- `clear cache` 를 적용하여 빌드
- ```eas build -p android --profile preview --clear-cache```
- ```eas build --platform ios --profile development --clear-cache --local```
