---
layout: post
title: Next 23장 - css 중첩 에러
author: admin
date: 2024-05-13 00:00:00 +900
lastmod: 2024-05-13 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [NEXT]
tags: [next, style, error]
---

# Next

## style error

- 기존의 next13 을 사용했던 사이드 프로젝트를 14로 올리는 과정에서 발생한 에러
- next 13에서 사용헀던 mui, styled-components를 tailwind로 마이그레이션 하는 과정에서 아래와 같은 에러가 발생했다.

```
http://localhost:3000/_next/static/css/app/child/page.css?v=1687197477239 was preloaded using link preload but not used within a few seconds from the window's load event. Please make sure it has an appropriate `as` value and it is preloaded intentionally.
```

- 에러 메세지는 위의 메서지와 다르지만 `Please make sure it has an appropriate as value and it is preloaded intentionally.`이 핵심이었다.
- 메세지만 봤을 땐, 성능 최적화를 위해 사전 로드된 리소스에 관한 것으로, 리소스를 사전 로드하는 것은 좋지만 불필요한 리소스에 관한 것은 성능에 악영향을 끼칠 수 있다는 내용이었다.
- `as`키워드를 중점으로 에러에 관한 여러 글들을 둘러보았지만, 스타일을 로드할 때 `<link as...>` 키워드를 추가하는 등의 해결 방법 모두 작동하지 않았다.
- 또한 스타일이 페이지 안에서 계속 로드되는 현상이 있었는데, `Link` 태그에 마우스를 올렸을 때 등 여러 상황에서 `css`가 중첩되어 로드되고 있었다.
- `next.config, package, tsconfig` 등 여러 파일들을 둘러보았지만 이것도 문제를 발견하지 못 했고, 다른 사이드 프로젝트를 `tailwind`로 마이그레이션 하는 과정에서도 문제가 없었기에 처음 보는 에러와 씨름했다.
- 해외 커뮤니티를 보던 중 next 13의 특정 버전에 따른 오류이며, `24/05/13` 기준으로도 아직 해결되지 않았다는 글을 보았고 14 버전으로 재설치했을 때 에러가 사라졌다...
