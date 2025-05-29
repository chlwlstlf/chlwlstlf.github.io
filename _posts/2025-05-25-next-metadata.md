---
layout: single
title: "[Next.js] Metadata 설정하기"
categories: next
toc: true
toc_sticky: true
---

# Next에서 metadata, og 태그, 공유, 홈 화면 추가 세팅

<br>

**관련 PR**  
[[feat] metadata 추가(#162) #164](https://github.com/ConSeat/frontend/pull/164)  
[[fix] 기본 메타데이터 안 나오는 오류 해결](https://github.com/ConSeat/frontend/pull/170)  
[[fix] 동적 생성 og 데이터 안 보이는 이슈(#171) #172](https://github.com/ConSeat/frontend/pull/172)  
[[fix] 아이폰 카카오 공유하기 오류 해결(#173) #174](https://github.com/ConSeat/frontend/pull/174)

<br>

## <mark class="pink">🔥메타데이터 정보 상수화</mark>

참고: [Next.js에서 동적으로 메타데이터와 오픈그래프 적용하기](https://velog.io/@doeunnkimm_/%EB%8F%99%EC%A0%81%EC%9C%BC%EB%A1%9C-mete%EC%99%80-og%ED%83%9C%EA%B7%B8-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0-opengraph-image%EA%B9%8C%EC%A7%80)

**<mark class="yellow">metadata.ts</mark>**

우리 사이트의 기본 metadata를 상수화하였다.

```ts
export const META = {
  title: "CON:SEAT - See it before you Sit",
  siteName: "CON:SEAT | 콘시트",
  shortName: "CON:SEAT", // 홈 화면 추가할 때 사용
  description: "한눈에 확인하는 콘서트장 시야",
  keyword: [
    "콘시트",
    "conseat",
    "concert seat",
    "콘서트",
    "콘서트 시야",
    "시야",
    "KSPO DOME",
    "체조 경기장",
    "올림픽 체조 경기장",
  ],
  url: "https://concertseat.site",
  locale: "ko_KR",
  googleVerification: "---",
  ogImage: "/og/main-og.png", // public/og/main-og.png 가져오기
  twitterSite: "@con_see_at",
  twitterCreator: "@con_see_at",
} as const;
```

<br>

**<mark class="yellow">googleVerification 얻는 방법</mark>**

**1\. 아래 사이트로 이동**  
[https://search.google.com/search-console](https://search.google.com/search-console)

<br>

**2\. 도메인 추가**  
"URL 접두어"로 사이트 url 추가(도메인도 상관없음)

- 당장 빠르게 시작해서 /blog, /app 등 특정 경로만 보면 된다 -> URL 접두어
- 앞으로 서브도메인·프로토콜을 모두 커버하고 싶고, DNS에도 손댈 수 있다 -> 도메인 속성

<br>

**3\. HTML 파일 다운**

- "DNS 레코드를 통해 도메인 소유권 확인" 창의 3번 항복에서 "복사" 버튼을 클릭해 TXT 레코드를 복사
- 이미 등록이 되어 있다면 좌측 상단 "햄버거 버튼" -> "설정" -> "소유권 인증" -> "도메인 이름 공급업체" -> TXT 복사

<br>
<br>

## <mark class="pink">🔥메타데이터 만드는 함수</mark>

**<mark class="yellow">getMetadata.ts</mark>**

**[인자]**

- `title`: title, og:title, og:siteName, og:images:alt, twitter:title, twitter:images:alt
- `description`: description, og:description, twitter:description
- `asPath`: alternates.canonical, og:url
- `ogImage`: og:image, twitter:image

```ts
import type { Metadata } from "next";
import { META } from "@/constants/metadata";

interface GenerateMetadataProps {
  title?: string;
  description?: string;
  asPath?: string; // 동적 메타데이터 생성 시 필수 입력
  ogImage?: string;
}

export const getMetadata = (metadataProps?: GenerateMetadataProps) => {
  const { title, description, asPath, ogImage } = metadataProps || {};

  const TITLE = title ? title : META.title;
  const DESCRIPTION = description || META.description;
  const PAGE_URL = asPath ? asPath : META.url;
  const OG_IMAGE = ogImage || META.ogImage;

  const metadata: Metadata = {
    metadataBase: new URL(META.url),
    alternates: {
      canonical: PAGE_URL, // 다른 URL, 같은 화면일 때 URL을 한 곳으로 몰아 SEO 점수 높임
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
      images: [
        {
          url: OG_IMAGE,
          width: 1200,
          height: 630,
          alt: TITLE,
        },
      ],
    },
    verification: {
      google: META.googleVerification,
    },
    twitter: {
      card: "summary", // summary_large_image, app, player
      site: META.twitterSite,
      creator: META.twitterCreator,
      title: TITLE,
      description: DESCRIPTION,
      images: [
        {
          url: OG_IMAGE,
          width: 1200,
          height: 630,
          alt: TITLE,
        },
      ],
    },
    appleWebApp: {
      capable: true, // standalone 모드 허용
      title: META.shortName, // 홈 화면 아이콘 아래에 표시될 이름
      statusBarStyle: "default", // 앱 실행 시 상단(status bar) 스타일
    },
  };

  return metadata;
};
```

<br>

**<mark class="yellow">layout.ts에 메타데이터 추가</mark>**

```ts
import { getMetadata } from "@/utils/getMetadata";

export const metadata: Metadata = getMetadata(); // metadata 추가

const RootLayout = async ({
  children,
}: Readonly<{ children: React.ReactNode }>) => {};
```

<br>
<br>

## <mark class="pink">🔥1. 웹 사이트 title</mark>

각 메타데이터 title에 맞게 잘 바뀐다.

**<mark class="yellow">[결과 화면]</mark>**

**[홈 페이지]**

![Image](https://github.com/user-attachments/assets/da7e53d9-2279-426b-82f6-a25de8be47f2)

**[시야 결과 페이지]**

![Image](https://github.com/user-attachments/assets/987abdfe-7109-4b38-abfc-ef7f1a92b36f)

<br>
<br>

## <mark class="pink">🔥2-1. 카카오톡 홈 페이지 링크 전송</mark>

**<mark class="yellow">[문제점]</mark>**  
제일 처음 페이지를 전송했을 때 og 이미지가 보이지 않았다.

![Image](https://github.com/user-attachments/assets/26a76b2a-b03b-45c3-a424-ef23e32ac31c)

<br>

**<mark class="yellow">[원인]</mark>**  
Facebook, Twitter, 카카오 등은 SVG를 제대로 렌더링하지 않거나 무시해 버린다.

<br>

**<mark class="yellow">[해결 방법]</mark>**  
og 이미지를 SVG에서 PNG로 변환하여 해결했다.

![Image](https://github.com/user-attachments/assets/8ad31e67-98f9-43c6-82d0-c83c11cab2e1)

<br>

**<mark class="yellow">[추가사항]</mark>**  
로고 해상도가 깨져보여서 피그마에서 `2x`로 추출했다.

<br>
<br>

## <mark class="pink">🔥2-2. 카카오톡 결과 페이지 링크 전송</mark>

**<mark class="yellow">[구현 사항]</mark>**  
결과 페이지는 동적으로 메타데이터를 만들어야 하는 페이지다. 기획 내용에 맞게 변경했다. `generateMetadata`만 페이지 파일에 적어두면 동적으로 메타데이터가 생성된다.

아까 만들어두었던 `getMetadata` 함수를 사용하여 변경해보자.

**home/[stadiumId]/single/[seatingId]/page.tsx**

```tsx
import { META } from "@/constants/metadata";
import { getMetadata } from "@/utils/getMetadata";

export async function generateMetadata({ params }): Promise<Metadata> {
  const { stadiumId, seatingId } = await params;

  const { data: stadiumList } = await getStadiumList();
  const seatInfo = await getSeatingReviews(Number(seatingId));
  const stadium = stadiumList.active?.find(
    (s) => s.stadiumId === Number(stadiumId)
  );

  if (!stadium) notFound();

  const title = `${stadium.stadiumName} | ${seatInfo.floorName} ${
    seatInfo.sectionName
  }${seatInfo.seatingName ? ` ${seatInfo.seatingName}` : ""} 시야`;
  const description = "CON:SEAT에서 구역별 시야를 확인해보세요";
  const ogImage = seatInfo.reviews?.[0]?.images?.[0] || undefined;

  return getMetadata({
    title,
    description,
    ogImage,
  });
}

const ResultPage = async ({ params }) => {};
```

<br>

**<mark class="yellow">[문제점]</mark>**  
동적으로 변경해주었는데 기본 메타데이터로 나왔다.

![Image](https://github.com/user-attachments/assets/ee5f720b-b791-4bab-8de6-de4eb16d859e)

<br>

**<mark class="yellow">[원인]</mark>**  
카카오 디벨로퍼 문의글에서 해당 내용을 찾을 수 있었다. 스크랩할 주소와 `og:url` 설정이 다른 경우 `og:url` 주소의 메타 정보를 조회한다.

현재 `og:url`는 default 값인 `https://concertseat.site` 이고, 이 url의 메타데이터인 default 값이 보였던 것이다.

![Image](https://github.com/user-attachments/assets/a66f2f02-b724-4f95-af02-77b4d7b8b421)

<br>

**<mark class="yellow">[해결방법]</mark>**

**1\. `og:url`의 값을 바꿔주는 `asPath` 값을 이 페이지 url로 변경**

```ts
export async function generateMetadata({ params }): Promise<Metadata> {
  // ···

  const title = `[${stadium.stadiumName}] ${seatInfo.floorName} ${
    seatInfo.sectionName
  }${seatInfo.seatingName ? ` ${seatInfo.seatingName}` : ""} 시야`;
  const description = "CON:SEAT에서 구역별 시야를 확인해보세요";
  const asPath = `${META.url}/home/${stadiumId}/single/${seatingId}`; // asPath 현재 경로로 정의
  const ogImage = seatInfo.reviews?.[0]?.images?.[0] || undefined;

  return getMetadata({
    title,
    description,
    asPath, // asPath 추가
    ogImage,
  });
}
```

<br>

**2\. 캐싱 제거**

그래도 이전 og 태그가 보인다면 이는 **캐싱**되어 있는 것이다. 빠른 og 정보를 가져오기 위해 카카오가 캐싱한다. 함수 내용이 바뀌었다면 캐싱을 수동으로 제거해줘야 한다.

아래 사이트에서 사이트 url을 넣고 "디버그" -> "캐시 초기화"를 눌러 캐시를 제거한다.

[https://developers.kakao.com/tool/debugger/sharing](https://developers.kakao.com/tool/debugger/sharing)

<br>
<br>

## <mark class="pink">🔥2-3. 카카오톡 결과 공유하기</mark>

**<mark class="yellow">[구현 사항]</mark>**

**1\. KakaoScript.tsx 생성**

앱 라우터에서 Kakao.init이 제대로 실행되지 않았다. `KakaoScript.tsx`를 클라이언트 컴포넌트로 변경해준 후 스크립트를 다 불러오는 즉시 Kakao.init을 적용시켜 주는 방법으로 해결하였다.

```ts
"use client";

import Script from "next/script";
import { PUBLIC_ENV } from "@/config/env";

const KakaoScript = () => {
  const onLoad = () => {
    const Kakao = window.Kakao;
    Kakao.init(PUBLIC_ENV.kakaoApiKey); // 카카오 디벨로버 JavaScript 키
  };

  return (
    <Script
      src="https://developers.kakao.com/sdk/js/kakao.js"
      async
      onLoad={onLoad}
    />
  );
};

export default KakaoScript;
```

<br>

**2\. 공유하기 정보 커스텀하기**

- `window.Kakao.Share`: Link에서 Share로 변경됨
- 메시지 구성 방법: 카카오 디벨로퍼에서 확인 가능('sendDefault()' \| 'sendCustom()' \| 'sendScrap()')
- 기본 메시지 템플릿(`objectType`): 카카오 디벨로퍼에서 확인 가능('feed' \| 'list' \| 'location' \| 'commerce' \| 'text')

![Image](https://github.com/user-attachments/assets/50708bda-8e13-48bd-988f-37c010af6f20)

<br>

**ShareArea.tsx**

```tsx
const handleShareKakao = () => {
  window.Kakao.Share.sendDefault({
    //2022.05.30부터 Link에서 Share로 변경
    objectType: "feed",
    content: {
      title,
      description,
      imageUrl,
      link: {
        mobileWebUrl: url,
        webUrl: url,
      },
    },
    buttons: [
      {
        title: "자세히 보기",
        link: {
          mobileWebUrl: url,
          webUrl: url,
        },
      },
    ],
  });
};
```

<br>

**<mark class="yellow">[결과 화면]</mark>**

![Image](https://github.com/user-attachments/assets/3886283e-344b-4ff4-babe-18c16558eff1)

<br>
<br>

## <mark class="pink">🔥3-1. 트위터 홈 페이지 링크 전송</mark>

참고 : [About X Cards](https://developer.x.com/en/docs/x-for-websites/cards/overview/abouts-cards)

**<mark class="yellow">[구현 사항]</mark>**

- `twitter:card`: 필수 값(`summary`, `summary_large_image`)
- summary에서 카드의 이미지는 최소 144x144 픽셀 또는 최대 4096x4096 픽셀의 1:1 종횡비를 지원한다.
- JPG, PNG, WEBP, GIF 형식이 지원된다. SVG는 지원되지 않는다.

```ts
twitter: {
  card: "summary", // summary_large_image, app, player
  site: META.twitterSite,
  creator: META.twitterCreator,
  title: TITLE,
  description: DESCRIPTION,
  images: [
    {
      url: OG_IMAGE, // png 형식
      width: 144,
      height: 144,
      alt: TITLE,
    },
  ],
},
```

<br>

**<mark class="yellow">twitter:card summary_large_image 예시</mark>**

![Image](https://github.com/user-attachments/assets/e6c35a33-a990-4872-a43b-dd4cb58ba022)

<br>

**<mark class="yellow">twitter:card summary 예시</mark>**

![Image](https://github.com/user-attachments/assets/d15fe2a6-1e58-4141-8e45-95dab1a14fa5)

<br>
<br>

## <mark class="pink">🔥3-2. 트위터 공유하기</mark>

**<mark class="yellow">[구현 사항]</mark>**  
`https://twitter.com/intent/tweet?url=` 뒤에 인코딩 된 url과 text를 넣으면 된다.

**ShareArea.tsx**

```tsx
const handleShareTwitter = () => {
  const text = encodeURIComponent(`${title}\n${description}\n`);
  window.open(
    `https://twitter.com/intent/tweet?url=${encodeURIComponent(
      url
    )}&text=${text}`,
    "_blank"
  );
};
```

<br>

**<mark class="yellow">[결과 화면]</mark>**  
정의한 text가 자동 기재된다.

![Image](https://github.com/user-attachments/assets/a2ea93f4-6134-4e29-aa84-18f1edaf7708)

<br>

**<mark class="yellow">[주의 사항]</mark>**  
트위터도 빠른 og 태그 렌더링을 위해 **캐싱**한다. 카카오는 캐시 초기화를 할 수 있지만 트위터는 1~3일마다 자체적으로 캐시를 초기화하는 것 같다. 바로 안 바뀌더라도 며칠 기다려보자😁

<br>
<br>

## <mark class="pink">🔥4. 홈 화면 추가</mark>

참고 : [favicon, icon, and apple-icon](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/app-icons)

**<mark class="yellow">[구현 사항]</mark>**

- `capable: true`: 홈 화면에 추가 시 주소창 없이 앱처럼 실행 홈 화면에 추가하면 사파리가 주소 표시줄·탭 바 없이 전체화면 모드로 실행
- `title`: 앱 이름
- `statusBarStyle: "default"`: 앱 실행 시 상단(status bar) 스타일  
  default: 투명 배경 + 다크 텍스트  
  black: 검은 배경 + 흰색 텍스트  
  black-translucent: 컨텐츠가 상태바 뒤까지 확장되고, 흰색 텍스트 사용

```ts
appleWebApp: {
  capable: true, // standalone 모드 허용
  title: META.title, // 홈 화면 아이콘 아래에 표시될 이름
  statusBarStyle: "default", // 앱 실행 시 상단(status bar) 스타일
},
```

<br>

**<mark class="yellow">[문제점]</mark>**

- title을 그대로 적었더니 앱 이름이 너무 길어졌다.
- 아이폰은 홈 화면 추가 아이콘을 따로 지정해주어야 한다.

**[아이폰 (문제)]**

![Image](https://github.com/user-attachments/assets/402bd62a-8e0b-458b-8669-883490b1099d)

<br>

**[갤럭시 (잘 나옴)]**

![Image](https://github.com/user-attachments/assets/a5e47dd7-706e-4084-b055-7f547392c815)

<br>

**<mark class="yellow">[해결방법]</mark>**

**1\. title 따로 지정**

```ts
appleWebApp: {
  capable: true,
  title: META.shortName, // CON:SEAT로 정의
  statusBarStyle: 'default',
},
```

<br>

**2\. apple icon 지정**

Next.js가 `app` 디렉터리 최상단에 특정 이름의 아이콘 파일을 두면, 별도 설정 없이도 자동으로 `<link>` 태그를 생성해 준다.

- `favicon.ico` : 브라우저 탭의 아이콘
- `icon.png` : PWA, 브라우저의 마스커블 아이콘
- `apple-icon.png` : iOS 사파리에서 “홈 화면에 추가” 했을 때 사용하는 Apple Touch Icon

![Image](https://github.com/user-attachments/assets/07459da4-f429-4b66-9c95-10a0f4745300)

<br>

**<mark class="yellow">[결과 화면]</mark>**

![Image](https://github.com/user-attachments/assets/1c26245f-9a4f-4822-a41f-0eef5dbc3290)

<br>
<br>

## <mark class="pink">🔥5. 슬랙 & 디스코드</mark>

**[슬랙]**

![Image](https://github.com/user-attachments/assets/272892cf-56ec-4085-a84e-650f2b4d4230)

<br>

**[디스코드]**

![Image](https://github.com/user-attachments/assets/5803b812-6a7e-47f1-b0d7-0d4545828bfd)

<br>
<br>

## <mark class="pink">🔥마무리</mark>

Next에서 중요한 SEO와 관련된 부분이라 많은 신경을 썼다. 또 카카오와 트위터 공유하기 기능도 있었기에 디자이너와 PM과 계속 대화를 나누며 문구와 사진을 변경했다.

트러블 슈팅을 굉장히 많이 한 것 같은데 누군가에게 도움이 되길 바란다.🤗
