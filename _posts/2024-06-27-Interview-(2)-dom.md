---
layout: post
title: Interview 25장 - DOM
author: admin
date: 2024-06-27 00:00:00 +900
lastmod: 2024-06-27 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [INTERVIEW]
tags: [dom]
---

# Interview

## DOM

- html, XML 같은 마크업 언어로 작성된 문서를 자바스크립트같은 프로그래밍 언어가 조작할 수 있도록 하는 인터페이스이다.
- 마크업 언어에는 자바스크립트가 접근 가능한 인터페이스가 없기 떄문에 문서에 접근하고 제어할 수 있는 수단이 필요하다.
  - 이벤트 핸들러를 추가, 새로운 요소를 추가 또는 삭제
- 돔은 계층적 구조로 표현되는데 노드들 간의 관게를 명확히 정의할 수 있고, 이벤트 처리에 유리한 구조이기 떄문이다.
- 이벤트가 부모에게, 부모가 자식에게 발생하는 이벤트 버블링, 이벤트 캡처링 등을 처리할 때 노드 구조가 효율 적이기 때문이다.
