---
layout: post
title: Node 2장 - Express (1)
author: admin
date: 2023-08-02 00:00:00 +900
lastmod: 2023-08-02 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [NODE]
tags: [node, express, server]
---

# Node

## 서버 작성

-

```js
const http = require("http");
const url = require("url");

const server = http.createServer((req, res) => {
  const parseUrl = url.parse(req.url, true);
  const path = parseUrl.pathname;
  const query = parseUrl.query;

  if (path === "/") {
    res.writeHead(200, { "Content-Type": "text-plain" });
    res.end("Here is Node.js");
  } else if (path === "/about") {
    res.writeHead(200, { "Content-Type": "text-plain" });
    res.end("Here is example");
  } else if (path.startWith("/user/")) {
    const userId = path.substring(6);
    res.writeHead(200, { "Content-Type": "text-plain" });
    res.end(`UserId : ${userId}`);
  } else {
    res.writeHead(404, { "Content-Type": "text-plain" });
    res.end("Not Works");
  }
});

const port = 3001;
server.listen(port, () => {
  console.log("Start Node.js Server");
});
```

## Express

- Node.js 기반의 어플리케이션을 쉽게 구축할 수 있도록 도와주는 프레임워크이다.
- 서버를 구축하고 라우팅, 미들웨어 등의 다양한 기능을 지원하여 쉽게 웹 애플리케이션을 구축할 수 있다.
- Node.js 보다 서버 구축을 위한 코드가 간단하다.

```js
// Express 사용 예시
const express = require("express");
const cors = require("cors");
const app = express();
const port = 3001;

app.use(cors);
app.use(express.json());

app.listen(port, () => {
  console.log("listening on port 3001");
});

app.get("/", (req, res) => {
  res.status(200).send("연결되었습니다.");
});

app.get("/user/:id", (req, res) => {
  const userId = req.params.id;
  res.send(`userId = ${userId}`);
});
```

- 이처럼 보다 간결한 코드를 구현할 수 있으며 다양한 미들웨어를 사용할 수 있다.
  - `cors, body-parser...`

### require...

- 설치한 라이브러리를 불러온다.
- 추가적으로 많이 사용되는 것은 `cors, body-parser...` 등이 있다.
- `express`인 `app`에서, `cors`를 사용하겠다는 코드이다.
- `ES6`인 `import`로도 사용이 가능하다.

```js
// CommonJS
const express = require('express');

// ES6
import "express" from "express";
```

### listen

- 포트 넘버와 콜백 함수를 인자로 받아서 서버를 실행하고, 클라이언트 요청에 대해 받아들일 준비를 한다고 생각하면 된다.
- 쉽게 말해 서버를 실행한다. 미들웨어

- 운영체제와 응용프로그램 사이에서 실행되는 소프트웨어이다.
- Express에서는 요청과 응답 사이에서 특정 목적을 위해 실행되는 메서드를 의미한다.

```js
app.use(express.json()); //1
app.use("/", express.static(path.join(__dirname, "public"))); //2
app.get("/", (req, res, next) => next()); //3
```

### 라우팅

- url 요청에 따른 응답 처리를 의미한다.

```js
app.get("/", (req, res, next) => res.send(200));
```

#### 예시

```js
app.use(express.json());
app.use(express.unlencoded({extended: false}));
app.use('/', express.static(path.join(__dirname, "public")));

app.get("/", (req, res) => {
  res.send("");
})

app.use((req, res, next) => {
  console.log(req)l
  const err = new Error("");
  error.status = 404;
  next(error);
})

app.use((err, req, res, next) => {
  console.log(err);
  res.render("err", err);
})
```
