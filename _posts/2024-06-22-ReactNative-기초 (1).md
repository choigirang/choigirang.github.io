---
layout: post
title: React Native 2장 - 기초
author: admin
date: 2024-06-22 00:00:00 +900
lastmod: 2024-06-22  00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT-NATIVE]
tags: [react, native, components, core]
---

# React Native

- [React Native](https://reactnative.dev/docs)
- API 문서를 보며 작성해보는 네이티브 기초

## 기본 개념

- 안드로이드에서는 Kotlin 또는 Java로 작성되고, IOS에서는 Swift 또는 Objective-C를 사용하여 개발된다.
- 하지만 리액트 네이티브를 사용하면 자바스크립트를 통해 리액트 컴포넌트 호출이 가능하다.
- 리액트 네이티브는 런타임 시에 해당 컴포넌트에 대해 안드로이드와 IOS 뷰를 생성하며 작동한다.

## 코어 컴포넌트

- [리액트 네이티브 코어 컴포넌트](https://reactnative.dev/docs/components-and-apis#user-interface)
- 리액트 네이티브는 많은 컴포넌트를 제공한다.
- 리액트 네이티브에서 작성된 컴포넌트는 런타임에서 실행 환경에 맞게 뷰를 생성한다.
- 워낙 많은 `props`가 있기 때문에 이에 대해선 문서 확인 필요

### 기본

#### View

- [View](https://reactnative.dev/docs/view)
- 가장 기본적인 Container로, flex, 스타일, 일부 터치, 접근성 컨트롤을 지원한다.
- `View`안에는 또 다른 `View`가 와도 된다.
  - 안드로이드 : `<ViewGroup>`
  - IOS : `<UIView>`
  - WEB : `<div>`

#### Text

- [Text](https://reactnative.dev/docs/text)
- 텍스트를 나타낼 때 사용되며, 중첩, 스타일링, 터치 등을 지원한다.
- 웹과 마찬가지로, `Text`에는 중첩된 스타일링이 적용된다.
- 안드로이드(NSAttributedString), IOS(NSAttributedString)을 사용하여 폰트,컬러 등을 설정해야 하지만, 리액트 네이티브에는 `fontWeight: 'bold'`,`color: 'red'` 등으로 바로 사용이 가능하다.
- `Text 컴포넌트끼리의 중첩은 가능하나, FlexBox 등의 사용은 불가하다.`
- 만약 `View` 컴포넌트 안에 2개의 컴포넌트가 존재 시, 이는 `block` 형태를 갖고, `Text` 컴포넌트 안에 2개의 컴포넌트가 존재 시, 이는 `inline` 형태를 갖는다.
- 컴포넌트는 단독으로 사용될 수 없고, `View` 안에 위치해야 한다.

#### TextInput

- [TextInput](https://reactnative.dev/docs/textinput)
- 텍스트를 작성하는 입력 요소이다.
- `input` 요소처럼 `placeHolder, value` 등을 사용할 수 있으며, `keyboardType` 정의가 가능하다.
- `onChangeText, onSubmitEditing, onFocus` 등의 이벤트가 있다.
- `textarea`와 같이 `multiline`을 사용할 수 있다.
- `multiline` 적용 시, `borderLeft, borderBottom`과 같은 일부 스타일로만 적용할 수 없으며, 이와 같은 스타일을 얻기 위해선 `View`컴포넌트를 활용할 수 있다.

```jsx
// can not apply
<TextInput
  style={{borderLeftColor: "#000000", borderLeftWidth: 2}}
  mutiline
  onChangeText={(text) => setText(text)}
/>

// can apply
<View style={{borderLeftColor: "#000000", borderLeftWidth: 2}}>
  <TextInput
  mutiline
  onChangeText={(text) => setText(text)}
  />
</View>
```

#### Image

- [Image](https://reactnative.dev/docs/image)
- 이미지를 나타낼 때 사용되며, 로컬 이미지, 네트워크 이미지 등 다양한 형태로 사용 가능하다.

```jsx
<View>
  <Image source={require(`@example.../...png`)} />
  <Image source={{ url: "https://example..." }} />
  <Image source={{ url: "data:image/png;base64..." }} />
</View>
```

#### ScrollView

- [ScrollView](https://reactnative.dev/docs/scrollview)
-
