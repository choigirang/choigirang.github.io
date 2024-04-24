---
layout: post
title: Next 22장 - SEO
author: admin
date: 2024-04-24 00:00:00 +900
lastmod: 2024-04-24 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [NEXT]
tags: [next, meta, seo]
---

# Next

## SEO

- 검색 엔진 최적화인 SEO, Next에서는 SSR로 동작하기 때문에 빈 태그가 아닌 실제 요소들이 들어가 있고, 그렇기에 SEO에 대한 강점을 가진다.
- 이 외에도 여러 가지의 평가 항목 중 `meta data`를 활용하여 검색 엔진에 우위를 가질 수 있다.
- Next 14 버전에서는 `<Head>` 태그를 사용하는 것이 아닌, **next에 내장된 MetaData를 활용하는 것으로 바뀌었다.**

### Meta data

- [Next Metadata](https://nextjs.org/docs/app/building-your-application/optimizing/metadata)
- 페이지 별로 메타 데이터를 작성할 수 있으며, 동적 메타 데이터 작성도 가능하다.
- 공식 문서에 나와있는대로 **generateMetadata를 이용해서 data를 가져온다.**

```jsx
import {Metadata} from "next";

export const meatadata : Metadata = {
  title: ...,
  description: ...,
}
```

```jsx
// dynamic metadata
import type { Metadata, ResolvingMetadata } from 'next'

type Props = {
  params: { id: string }
  searchParams: { [key: string]: string | string[] | undefined }
}

export async function generateMetadata(
  { params, searchParams }: Props,
  parent: ResolvingMetadata
): Promise<Metadata> {
  // read route params
  const id = params.id
  const product = await fetch(`https://.../${id}`).then((res) => res.json())

  const previousImages = (await parent).openGraph?.images || []

  return {
    title: product.title,
    openGraph: {
      images: ['/some-specific-page-image.jpg', ...previousImages],
    },
  }
}

export default function Page({ params, searchParams }: Props) {}
```

### meta data custom

- 위와 같이 동적으로 메타 데이터를 받아와서 사용하는 것 외에도, 페이지별 동일한 메타 데이터가 반복되어 사용된다면 아래와 같이 상수를 이용하여 사용할 수 있다.
- 이후 페이지별로 metadata를 넘겨주기만 하면 된다.

```jsx
import { Metadata } from "next";

interface MetadataProps {
  [key: string]: string;
}

export const META = {
  title: "...",
  siteName: "...",
  description: "...",
  keyword: ["...", "...", "...", "..."],
  url: "https://...",
  googleVerification: process.env.GOOGLE || "",
  naverVerification: process.env.NAVER || "",
  ogImage: "/main.png",
} as const;

export const getMetadata = (metadataProps?: MetadataProps) => {
  const { title, description, asPath } = metadataProps || {};

  const TITLE = title ? `${title} | ...` : META.title;
  const DESCRIPTION = description || META.description;
  const PAGE_URL = asPath ? META.url + asPath : META.url;
  const OG_IMAGE = META.ogImage;

  const metadata: Metadata = {
    metadataBase: new URL(META.url),
    alternates: {
      canonical: PAGE_URL,
    },
    title: TITLE,
    description: DESCRIPTION,
    keywords: [...META.keyword],
    openGraph: {
      title: TITLE,
      description: DESCRIPTION,
      siteName: TITLE,
      locale: "ko_KR",
      type: "website",
      url: PAGE_URL,
      images: {
        url: OG_IMAGE,
      },
    },
    verification: {
      google: META.googleVerification,
      other: {
        "naver-site-verification": META.naverVerification,
      },
    },
    twitter: {
      title: TITLE,
      description: DESCRIPTION,
      images: {
        url: OG_IMAGE,
      },
    },
  };

  return metadata;
};
```

```jsx
// home/page.tsx
import { Metadata } from "next";
import { getMetadata } from "@/constant/metaData";

export const generateMetadata = async (): Promise<Metadata> => {
  return getMetadata();
};

// example/page.tsx
export const generateMetadata = async (): Promise<Metadata> => {
  return getMetadata({ title: "여기는 example", description: "예시 페이지" });
};
```

### web master

- [next metadata vertification](https://nextjs.org/docs/app/api-reference/functions/generate-metadata#verification)
- [네이버 서치 어드바이저](https://searchadvisor.naver.com/)
- 네이버, 구글 등의 검색 엔진에 노출되고 싶다면 각 사이트의 웹마스터 도구를 사용할 수 있다.
- 각 사이트마다 웹 마스터 도구에 들어가면 본인의 사이트인지에 대한 확인 작업을 거친다.
- 이러한 확인 작업은 사이트에서 부여하는 고유의 문자열을 `meta`태그에 넣어 확인하거나, 다운로드 되는 html 파일을 `public` 파일에 넣어 배포하고, 검색 엔진에서 부여한 코드 혹은 파일을 확인하는 작업을 거친다.

### next-sitemap

- site map이란, 구글이나 네이버와 같은 검색 사이트들의 크롤링 봇에게 본인의 서비스에서 사용할 수 있는 사이트 주소를 알려주는 것이다.
- 이러한 작업은 운영하는 서비스가 사용자들의 검색어에 더 많이 노출될 수 있도록 도와주는 이점을 갖는다.
- 상황에 따라서 노출되면 안 되는 특정 주소는 **robotos.txt를 이용하여 크롤링해도 되는 주소와 하지 말아야 할 주소를 구분하여 명시해줄 수 있다.**

```
npm i next-sitemap
```

```jsx
// pacakge.json
{
  "scripts": {
    "postbuild": "next-sitemap",
  },
}
```

```jsx
// /app/next-sitemap.config.js 파일 생성
/** @type {import('next-sitemap').IConfig} */

module.exports = {
  siteUrl: "http://localhost:8088", // .게시하는 site의 url
  generateRobotsTxt: true, // robots.txt generate 여부 (자동생성 여부)
  sitemapSize: 7000, // sitemap별 최대 크기 (최대 크기가 넘어갈 경우 복수개의 sitemap으로 분리됨)
  changefreq: "daily", // 페이지 주소 변경 빈도 (검색엔진에 제공됨) - always, daily, hourly, monthly, never, weekly, yearly 중 택 1
  priority: 1, // 페이지 주소 우선순위 (검색엔진에 제공됨, 우선순위가 높은 순서대로 크롤링함)
  exclude: [
    "/exclude/review", // 페이지 주소 하나만 제외시키는 경우
    "/exclude/**", // 하위 주소 전체를 제외시키는 경우
  ], // sitemap 등록 제외 페이지 주소
  robotsTxtOptions: {
    // 정책 설정
    policies: [
      {
        userAgent: "*", // 모든 agent 허용
        allow: "/", // 모든 페이지 주소 크롤링 허용
        disallow: [
          "/exclude", // exclude로 시작하는 페이지 주소 크롤링 금지
        ],
      },
      // 추가 정책이 필요할 경우 배열 요소로 추가 작성
    ],
  }, // robots.txt 옵션 설정
};
```
