---
layout: post
title: Git 3장 - Git Flow
author: admin
date: 2023-08-07 00:00:00 +900
lastmod: 2023-08-07 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [GIT]
tags: [git, github, switch, branch, flow]
---

# Git

## Git branch 다루기

### git flow init

- `git-flow`의 명령어들을 지속적으로 사용하기 위해 사용하는 명령어이다.
- 저장소들을 초기화하는 과정으로, 대체로 `git clone` 후에 최초 1회만 사용된다.
- 명령어 입력 시 branch 이름들을 어떻게 지정할 것인지 물어보는데 기본값으로 적용하는 것이 일반적이다.

### git checkout [branch]

- 다른 저장소에 접근하고 싶을 때 사용한다.

```js
git checkout dev/fe
Switched to branch 'dev/fe'
```

### git branch

- 로컬(사용자PC)의 저장소들을 확인할 때 사용한다.
- 최초 `git clone` 후에는 `master` 브랜치만 존재하며 `git flow init`명령어를 사용 후 `master, develop`이 존재하게 된다.
- 해당 명령어를 통해 사용자가 어떤 저장소에 접근 중인지 확인이 가능하다.

```js
git branch
* master
develop
```

### git branch -r

- 실제 서버에 존재하는 저장소 목록을 볼 수 있다.

### git branch [branch], git checkout -b [branch]

- 명령어 뒤에 붙는 저장소를 생성해준다.

### git branch -d [branch]

- 저장소를 삭제한다.
- 로컬에서만 삭제된다.
- 서버의 저장소는 삭제되지 않는다.
