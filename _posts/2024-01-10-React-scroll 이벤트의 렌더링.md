---
layout: post
title: React 67장 - scroll 이벤트의 렌더링
author: admin
date: 2024-01-10 00:00:00 +900
lastmod: 2024-01-10  00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags:
  [
    react,
    components,
    jsx,
    scroll,
    render,
    debounce,
    requestAnimationFrame,
    cancelAnimationFrame,
  ]
---

# React

[참고자료1](https://velog.io/@yrnana/scroll-event%EC%97%90-rAF-throttle%EC%9D%84-%EC%A0%81%EC%9A%A9%ED%95%B4%EC%95%BC%ED%95%A0%EA%B9%8C)
[참고자료2](https://marshallku.com/dev/%EC%8A%A4%ED%81%AC%EB%A1%A4-%EB%93%B1%EC%9D%98-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EC%B5%9C%EC%A0%81%ED%99%94%ED%95%98%EA%B8%B0)

## 스크롤 이벤트

- 스크롤 여부에 따라 `Nav bar`의 스타일이 달라지는 경우가 있다.
- `documnet`의 스크롤 여부를 판단하는 것에 대한 예제 코드이다.

```jsx
const [scroll, setScroll] = useState < boolean > false;

useEffect(() => {
  const scrollHandler = () => {
    if (window.scrollY >= 100) {
      setScroll(true);
    } else {
      setScroll(false);
    }
  };

  window.addEventListner("scroll", scrollHandler);

  return () => window.removeEventListner("scroll", scrollHandler);
}, []);

// 스크롤 상태값에 의한 스타일 지정
```

- 윈도우의 스크롤 이벤트가 발생할 때, `scrollHandler` 함수를 사용하여 스크롤에 따른 상태값을 변경한다.
- `removeEventListner`로 이벤트를 제거해주지 않을 시, 이벤트 리스너가 계속 추가되며 메모리에 영향을 끼칠 수 있기 때문에 이벤트 리스너를 해제해주어야 한다.

### 렌더링

- 스크롤의 좌푯값을 파악하여 상태값을 바꿔줄 수 있지만, 매 스크롤마다 렌더링이 발생한다.
- `useEffect`에 콘솔을 추가해보면 스크롤이 발생할 때마다 콘솔이 끊임없이 발생하는 것을 볼 수 있다.
- 불필요한 렌더링이 발생하기 때문에 이에 대한 해결 방법이 필요했다.

### 렌더링 개선

**useCallback**

- 가장 대표적인 방법이지만 `useEffect`안에서 사용할 수 없기 때문에 제외

**debounce**

- 이 함수는 일정 시간동안 이벤트의 반복 호출을 방지하는 방법이다.
- 분명히 활용에 용이한 곳이 있겠지만, 스크롤이 반복될 때에도 실행되야 하기 때문에 방법은 제외했다.
- 예제 코드는 아래와 같다.
- `debounce` 함수를 실행하고, 원하는 시간값을 입력한다.(ms 단위)
- 200ms동안 중복 이벤트가 발생되는 것을 방지한다.

```jsx
useEffect(() => {
  const scrollHandler = debounce(() => {
    if (window.scrollY >= 100) {
      setScroll(true);
    } else {
      setScroll(false);
    }
  }, 200);

  window.addEventListner("scroll", scrollHandler);

  return () => window.removeEventListner("scroll", scrollHandler);
}, [scroll]);
```

**requestAnimationFrame(callback),cancelAnimationFrame**

- 두 함수는 주로 애니메이션, 스크롤 이벤트와 같은 브라우저 애니메이션을 다룰 때 사용하는 함수이다.
- **requestAnimationFrame**은 브라우저에 실행하고 싶은 애니메이션을 알리고, 다음 리페인트가 진행되기 전에 `callback`을 실행해서 해당 애니메이터를 업데이트 하도록 한다.
- 스크롤을 읽는 작업을 현재 프레임에서 실행하고, 다음 렌더링이 발생할 때에 해당 콜백을 같이 넘겨주어 다음 프레임에서 콜백 함수를 같이 실행하도록 하기 떄문에 렌더링을 줄일 수 있다.

```jsx
useEffect(() => {
  let animationFrameId: number;

  const scrollHandler = () => {
    cancelAnimationFrame(animationFrameId);

    animationFrameId = requestAnimationFrame(() => {
      if (window.scrollY > 100 && !scroll) {
        setScroll(true);
      } else if (window.scrollY <= 100 && scroll) {
        setScroll(false);
      }
    });
  };

  window.addEventListener("scroll", scrollHandler);

  return () => {
    window.removeEventListener("scroll", scrollHandler);
    cancelAnimationFrame(animationFrameId);
  };
}, [scroll]);
```
