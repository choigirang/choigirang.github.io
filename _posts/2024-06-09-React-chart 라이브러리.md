---
layout: post
title: React 74장 - chart.js
author: admin
date: 2024-06-09 00:00:00 +900
lastmod: 2024-06-09  00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, chart]
---

# React

## Chart.js

- [공식문서](https://react-chartjs-2.js.org/examples/)
- 관련 자료가 많고 커스텀이 쉽다고 하여 차트를 그리는 여러가지 라이브러리 중 선택
- 공식문서에서 볼 수 있는 여러 가지 컴포넌트 중 본인이 사용할 컴포넌트를 가져온다.
- `data`라는 객체 안에 본인이 사용할 데이터에 대한 라벨이나 값을 설정한다.
- `registor()`는 사용할 여러 기능들을 포함한다.

  - 컴포넌트를 가져오는 것과 별도로 `registor`에 추가해주어야 한다.

  ```jsx
  import {
    Chart as ChartJS,
    BarElement,
    CategoryScale,
    LinearScale,
    Tooltip,
    Legend,
    PointElement,
    LineElement,
  } from "chart.js";
  import { Line } from "react-chartjs-2";

  ChartJS.register(
    BarElement, // 바 형태의 차트를 그리는데 사용되는 엘리먼트(추상화된 객체) 기능
    CategoryScale, // 차트의 x축이나 y축에 카테고리 형태의 레이블을 표시하는 스케일 기능
    LinearScale,
    Tooltip, //차트의 타이틀을 표시하는 기능
    Legend, // 차트에서 어떤 색, 모양, 형태의 라인이나 바가 어떤 데이터를 나타내는지 알려주는 기능
    PointElement,
    LineElement
  );

  const data = {
    labels,
    datasets: [...],
  }

  export default function Example(){
    return ...
  }
  ```

### options

- chart.js에는 다양한 옵션값이 존재한다.
- Bar 형태의 경우 두께를 정하거나, 좌측의 Y축 그래프에 대한 세부 설정이 가능하다.
- 타입 스크립트를 사용할 경우, chart.js의 `ChartOptions`에 어떤 형태인지 지정하고 아래와 같은 형식으로 작성할 수 있다.

```jsx
const options: ChartOptions<'bar'> = {
  scales: {
    y: {
      beginAtZero: true,
      ticks: {
        stepSize: 1,
        callback: function (value) {
          return Number(value).toFixed(0); // Convert to integer
        },
      },
    },
  },
};

export default function Graph(){
  ...
  return (
    <Bar data={data} options={options}/>
  )
}
```
