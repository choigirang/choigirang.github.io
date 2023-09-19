---
layout: post
title: HTML/CSS 5장 - canvas
author: admin
date: 2023-03-17 00:00:00 +900
lastmod: 2023-03-17 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [HTML/CSS]
tags: [html, css, canvas]
---

# Html/Css

## Canvas

- HTML의 `<canvas>` 태그와 JavaScript를 사용하면 다양한 그래픽 요소를 만들 수 있다.
- 도형을 그리는 것은 물론, 데이터 시각화, 애니메이션, 웹 게임 등 다양하게 활용이 가능하다.
- 캔버스는 canvas 엘리먼트를 DOM으로 조작하는 방식으로 작성되며, 사용할 `id`를 작성해주는 것이 좋다.

  ```html
  <canvas id="canvas">
    캔버스를 지원하지 않는 브라우저에서는 캔버스 대신 태그 사이 내용이 표시된다.
  </canvas>
  ```

- 자바스크립트를 사용해 엘리먼트를 선택한다.

  ```js
  const canvas = document.querySelector("#canvas");
  ```

- 캔버스를 다루기 위해 너비와 높이를 설정해주어야 한다.
- 크기를 설정해주지 않으면 기본적으로 300픽셀 \* 150픽셀의 사이즈로 생성된다.

  ```html
  <canvas id="canvas" width="500" height="500"></canvas>
  <canvas id="canvas" width="100vw" height="100vh"></canvas>
  /*캔버스는 px단위로만 인식하기 때문에 100vw,100vh를 작성해도 100px로
  인식한다.*/
  ```

- DOM으로 값을 설정할 수도 있다.

  ```js
  // 화면 크기에 맞춰서 설정
  canvas.width = window.innerWidth;
  canvas.height = window.innerHeight;

  // 화면 크기에 비례해서 설정
  canvas.width = window.innerWidth * 0.5;
  canvas.height = window.innerHeight * 0.5;

  // DOM으로 설정 시, vw,vh 등의 단위값을 지정할 수 있다.
  // style속성을 이용하여 변화를 주는 경우. px의 크기까지
  // 해당 단위에 빌해 확대되는 개념이기 때문에 특정한 경우가 아니면
  // 사용을 자제해야한다.
  canvas.style.width = "50vw";
  canvas.style.height = "50vh";
  ```

### 실습

- 색칠된 사각형 그리기

  ```js
  ctx.fillStyle = "blue";
  ```

  - `fillStyle` 속성으로 사각형 내부를 색칠할 색상을 정한다.

  ```js
  ctx.fillRext = (10, 10, 100, 50);
  // 전달 인자는 순서대로 x좌표 , y좌표 , 가로길이, 세로길이
  ```

  - `fillRext` 메서드로 사각형을 그려준다.

- 선으로만 그리기

  ```js
  ctx.lineWidth = 5;
  ctx.strokeStyle = "black";
  ```

  - `lineWidth` 속성으로 선의 굵기를, `strokeStyle` 속성으로 선의 색상을 설정한다.

  ```js
  ctx.strokeRect(10, 10, 100, 50);
  // 전달 인자는 순서대로 x좌표, y좌표, 가로길이, 세로길이
  ```

  - `strokeRect` 메서드로 사각형을 그려준다.

- 사각형으로 지우기

  ```js
  ctx.clearRect(20, 20, 80, 30);
  // 전달 인자는 동일하다.
  ```

  ![image](https://user-images.githubusercontent.com/118104644/225791707-92536247-226c-4b25-8475-d2de41c3f50e.png){:.center}

- 클릭할 때 캔버스 위에서의 마우스 위치를 구하려면, 화면에서의 마우스 위치에서 화면에서의 캔버스 위치를 빼면 된다.
- 화면상 마우스 위치를 구하는 이벤트 객체의 속성
  - X좌표 : `event.clientX`
  - Y좌표 : `event.clientY`
- 화면상 캔버스 위치를 구하는 속성
  - X좌표 : `event.clientX`
  - Y좌표 : `event.clientY`
- `event.offsetX`, `event.offsetY`로 바로 구할 수도 있다.

  ```js
  canvas.onclick = function (event) {
    const x = event.clientX - ctx.canvas.offsetLeft;
    const y = event.clientY - ctx.canvas.offsetTop;

    ctx.fillRect(x - 15, y - 15, 30, 30);
    // 클릭할 때마다 30픽셀 * 30픽셀 크기의 사각형을 그린다.
    // x,y를 그대로 전달하면 해당 좌표부터 사각형이 시작되어 어색하다.
    // 클릭한 위치를 사각형의 정중앙이 되게 하려면 사각형/2 한 만큼 좌표에서 빼주면 된다.
  };
  ```

[구현된 canvas 보기](https://codesandbox.io/s/sagaghyeonggeurigiibenteu-forked-8zn7o2?from-embed)}{:target="\_blank"}

[참고 내용 더 보기](https://www.w3schools.com/tags/ref_canvas.asp){:target="\_blank"}
