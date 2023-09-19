---
layout: post
title: Next 5장 - 프로젝트 "사이드 퀘스트" (2)
author: admin
date: 2023-06-02 00:00:00 +900
lastmod: 2023-06-02 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [NEXT]
tags: [next, react, useform, sweetalert]
---

# Next.js

## useForm

- 간편한 form을 구현하기 위해 React-Hook-Form 라이브러리를 사용했다.
- `npm i react-hook-form`을 사용하여 라이브러리를 설치한다.
- `useForm`은 여러가지 속성이 있다.
  - `register` : 상태를 변경하기 위한 함수이다. `<input {...register("name")} />` => `{name: 입력값}`
    - 옵션을 사용하여 유효성 검사도 진행할 수 있다. `register("name",{required:true, maxLength:2, ...})`
  - `watch` : 지정된 input을 관찰하고 그 값들을 반환한다. 리렌더가 발생한다.(getValue도 사용된다.)
  - `reset` : 입력폼을 초기화한다.
  - `handleSubmit` : submit에 대한 함수로 입력된 데이터를 제출하는 용도로 사용된다.

```tsx
import { useState } from "react";
import { useForm, submitHandler } from "react-hook-form";
import ReciveComponent from "/example";

type FormValues = {
  name: string;
  email?: string;
};

function Example() {
  const { register, watch, reset, handleSubmit } = useForm<FormValues>();
  const [sumitData, setSubmitData] = useState<FormValues | null>();
  const nowName = watch("name");

  const onSubmit: SubmitHandler<FormValues> = (data) => {
    fetch("...", data.name);
    reset();
  };

  return (
    <div>
      <form onSumit={handleSubmit(onSubmit)}>
        <input {...register("name")} />
        <imput {...register("email")} />

        <button type="submit">SUBMIT</button>
      </form>

      <ReciveComponent formData={submitData} />
    </div>
  );
}
```

## sweetAlert2

- 애니메이션 알림창 라이브러리로 alert와 confirm 등의 기능을 수행한다.
- `npm i sweetalert2`로 설치한다.
- 알림창, 확인창, 토스트 모달 등 다양한 기능을 구현할 수 있다.
- 버튼의 색상 변경 및 icon 변경 등의 다양한 속성을 갖고 있으며 유연하게 커스텀이 가능하다.

```tsx
import Swal from "sweetalert2";

const successAlert = (text: string) => {
  Swal.fire({
    text: text,
    icon: "success",
    confirmButtonText: "확인",
    confirmButtonColor: "rgb(165,220,134)",
  });
};

export const confirmAlert = (text: string, title: string) => {
  return new Promise<void>((resolve, reject) => {
    Swal.fire({
      title: text,
      icon: "warning",
      showCancelButton: true,
      confirmButtonColor: "rgb(165,220,134)",
      cancelButtonColor: "#F27474",
      confirmButtonText: "확인",
      cancelButtonText: "취소",
    }).then((result) => {
      if (result.isConfirmed) {
        Swal.fire({
          title: title.slice(0, -1),
          text: `${title} 완료되었습니다.`,
          icon: "success",
          confirmButtonText: "확인",
          confirmButtonColor: "rgb(165,220,134)",
        }).then(() => {
          resolve(); // 확인 버튼을 눌렀을 때 Promise를 resolve하여 완료됨을 알림
        });
      } else {
        Swal.fire({
          title: title.slice(0, -1),
          text: `${title} 취소되었습니다.`,
          icon: "error",
          confirmButtonText: "확인",
          confirmButtonColor: "#F27474",
        }).then(() => {
          reject(); // 취소 버튼을 눌렀을 때 Promise를 reject하여 취소됨을 알림
        });
      }
    });
  });
};

export const successToast = (text: string, callback?: () => void) => {
  Swal.fire({
    icon: "success",
    title: text,
    toast: true,
    position: "center",
    showConfirmButton: false,
    timer: 1000,
    timerProgressBar: true,
  }).then(() => {
    if (callback) {
      callback();
    }
  });
};

function Example() {
  const { register, watch, reset, handleSubmit } = useForm<FormValues>();
  const [sumitData, setSubmitData] = useState<FormValues | null>();
  const nowName = watch("name");

  const onSubmit: SubmitHandler<FormValues> = (data) => {
    fetch("...", data.name);
    reset();
    successAlert("제출이 완료되었습니다.");
  };

  return (
    <div>
      <form onSumit={handleSubmit(onSubmit)}>
        <input {...register("name")} />
        <imput {...register("email")} />

        <button type="submit">SUBMIT</button>
      </form>

      <ReciveComponent formData={submitData} />
    </div>
  );
}
```

## dynamic

- Next.js에서 지원하는 자바스크립트 모듈을 **동적**으로 가져와 사용할 수 있다.
- 문법으로 빌드 타임이 아닌 런타임에 불러오도록 한다.
- 초기 로딩부터 사용하지 않는 부분이 존재하는데, 개발자가 모든 것을 미리 로드하는 것이 아닌 필요에 따라 구성 요소 또는 모듈을 로드하기 때문에 초기 로딩 시간이 줄어들고 웹 사이트가 더 빨라진다.
- 필요한 코드만 로드하기 때문에 초기 번들 크기를 줄일 수 있으며 서버 성능 개선과 호스팅 비용 감소 등의 장점이 있다.
- 여러가지 옵션값을 사용할 수 있다.

```jsx
import dynamic from "next/dynamic";

export const Example = () => {
  const Ex = dynamic(import("../components/Hello"), {
    loading: () => <p>Loading...</p>,
    // ssr : false
  });
};
```
