---
layout: single
title: "[Next.js] Page Router 핵심 정리 - Middleware부터 렌더링까지"
categories: next
toc: true
toc_sticky: true
---

# Next.js Page Router 핵심 정리

## <mark class="pink">전체 실행 순서</mark>

요청 한 번이 들어올 때 순서는 크게 두 단계로 나뉜다.

### 1. 공식 라우팅 순서 — 어떤 페이지를 렌더링할지 결정

📎 [공식 문서 — Execution order](https://nextjs.org/docs/15/pages/api-reference/file-conventions/middleware#execution-order)

<img width="721" height="419" alt="Image" src="https://github.com/user-attachments/assets/cc126b4a-379f-4a46-9085-4f91a5fd8997" />

> Middleware는 프로젝트의 모든 라우트에 대해 실행됩니다. 따라서 특정 경로를 정확히 대상으로 하거나 제외하려면 matcher를 사용하는 것이 중요합니다. 실행 순서는 다음과 같습니다.

```
HTTP 요청
  │
  ▼
① next.config.mjs - headers
  │  → 응답 헤더 삽입 (CORS, Cache-Control 등)
  ▼
② next.config.mjs - redirects
  │  → 영구적 URL 리다이렉트 (301/308 등). Middleware보다 먼저 실행됨
  ▼
③ Middleware (기본: Edge Runtime)
  │  → rewrite/redirect/next 결정
  ▼
④ next.config.mjs - rewrites (beforeFiles)
  │  → 파일시스템 매칭 전 URL 재작성
  ▼
⑤ Filesystem 라우트 (public/, _next/static, pages/ 정적 경로)
  │  → 정적 파일 및 pages/ 내 고정 경로 파일 탐색
  ▼
⑥ next.config.mjs - rewrites (afterFiles)
  │  → 정적 파일 매칭 실패 시 URL 재작성
  ▼
⑦ Dynamic Routes (pages/ 동적 경로)
  │  → [slug], [...params] 등 동적 세그먼트 매칭
  ▼
⑧ next.config.mjs - rewrites (fallback)
     → 모든 매칭 실패 후 마지막 재작성 시도
```

### 2. SSR 렌더링 파이프라인 — 페이지 결정 이후 서버에서 HTML 생성

```
페이지 결정됨
  │
  ▼
① getServerSideProps / getStaticProps
  │  → 페이지 데이터 페칭. Next.js가 독립적으로 직접 호출
  │  → { props } / { redirect } / { notFound } 반환
  ▼
② _app.getInitialProps (GIP)
  │  → 공통 데이터 페칭
  │  → Component.getInitialProps 호출 (GSSP 페이지는 undefined, GSSP와 별도 실행)
  ▼
③ _document.tsx
  │  → <html>, <head>, <body> 구조 정의 및 스크립트 삽입 (서버 전용)
  ▼
④ _app.tsx 렌더링 → Component 렌더링
  │  → Provider 래핑 후 페이지 컴포넌트 렌더링
  ▼
⑤ 클라이언트 Hydration
     → 서버 HTML에 React 이벤트 연결, 인터랙티브 앱으로 전환
```

<br>
<br>

## <mark class="pink">각 파일/함수 상세</mark>

### <mark class="yellow">middleware.ts — 커스텀 로직 중 가장 먼저 실행</mark>

📎 [공식 문서 — Middleware](https://nextjs.org/docs/15/pages/api-reference/file-conventions/middleware)

- **런타임**: 기본적으로 Edge Runtime (V8 기반). Next.js v15.5부터 Node.js runtime도 선택 가능
- **위치**: `src/middleware.ts` (또는 프로젝트 루트)
- **실행 시점**: `next.config.mjs`의 headers/redirects 처리 이후, 파일시스템 매칭 전
- **역할**: URL 변환, 인증 체크, 로케일 처리, 헤더 주입

**반환값 3가지**

| 반환                      | 설명                                                         |
| ------------------------- | ------------------------------------------------------------ |
| `NextResponse.redirect()` | 다른 URL로 리다이렉트 (클라이언트가 새 URL로 재요청)         |
| `NextResponse.rewrite()`  | URL은 그대로이지만 내부적으로 다른 곳으로 전달 (투명 프록시) |
| `NextResponse.next()`     | 그냥 다음 단계로 통과                                        |

```ts
// redirect — 모든 요청을 /home으로 리다이렉트
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function middleware(request: NextRequest) {
  return NextResponse.redirect(new URL("/home", request.url));
}
```

```ts
// rewrite — 경로에 따라 다른 페이지로 투명하게 전달 (URL은 그대로)
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function middleware(request: NextRequest) {
  if (request.nextUrl.pathname.startsWith("/about")) {
    return NextResponse.rewrite(new URL("/about-2", request.url));
  }
  if (request.nextUrl.pathname.startsWith("/dashboard")) {
    return NextResponse.rewrite(new URL("/dashboard/user", request.url));
  }
}
```

```ts
// next — 요청/응답 헤더를 수정하고 다음 단계로 통과
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function middleware(request: NextRequest) {
  // 요청 헤더를 복제하고 새 헤더 `x-hello-from-middleware1` 추가
  const requestHeaders = new Headers(request.headers);
  requestHeaders.set("x-hello-from-middleware1", "hello");

  // NextResponse.next에 수정된 요청 헤더를 전달 (upstream에서 읽기 가능)
  const response = NextResponse.next({
    request: {
      // 수정된 요청 헤더
      headers: requestHeaders,
    },
  });

  // 응답 헤더에 `x-hello-from-middleware2` 추가 (클라이언트에서 읽기 가능)
  response.headers.set("x-hello-from-middleware2", "hello");
  return response;
}
```

<br>

**런타임 설정**

📎 [공식 문서 — Runtime](https://nextjs.org/docs/15/pages/api-reference/file-conventions/middleware#runtime)

<img width="726" height="329" alt="Image" src="https://github.com/user-attachments/assets/098c9e32-58ab-4dae-b559-4e6de52fe718" />

> Middleware는 기본적으로 Edge runtime을 사용합니다. v15.5부터 Node.js runtime도 지원합니다. 활성화하려면 middleware 파일의 config 객체에서 runtime을 `nodejs`로 설정하세요.

```ts
export const config = {
  runtime: "nodejs", // Node.js runtime 사용 시
};
```

<br>

**핵심 포인트**

- `config.matcher`를 export하지 않으면 모든 경로에 실행됨
- 기본 Edge Runtime에서는 Node.js API 사용 불가 (`fs`, `path` 등) → Node.js runtime으로 전환하면 사용 가능
- 쿠키/헤더 읽기·쓰기 가능

<br>

### <mark class="yellow">next.config.mjs — Rewrite & Redirect</mark>

**실행 시점**: Middleware 이후, 파일시스템 매칭 전후

**Rewrites 단계**

`beforeFiles` → 파일시스템 매칭 → `afterFiles` → `fallback`

| 단계          | 설명                                                 |
| ------------- | ---------------------------------------------------- |
| `beforeFiles` | 파일시스템보다 먼저 적용. 파일이 있어도 rewrite 가능 |
| `afterFiles`  | 파일시스템 매칭 실패 후 적용                         |
| `fallback`    | 모든 것이 실패한 후 마지막 시도                      |

```js
module.exports = {
  async rewrites() {
    return {
      beforeFiles: [
        // 파일시스템보다 먼저 적용 — pages/some-page.tsx가 있어도 이 rewrite가 먼저 실행됨
        {
          source: "/some-page",
          destination: "/somewhere-else",
          has: [{ type: "query", key: "overrideMe" }],
        },
      ],
      afterFiles: [
        // 파일시스템 매칭 실패 후 적용
        {
          source: "/non-existent",
          destination: "/somewhere-else",
        },
      ],
      fallback: [
        // afterFiles도 실패한 경우 마지막 시도 — 외부 사이트로 프록시
        {
          source: "/:path*",
          destination: `https://my-old-site.com/:path*`,
        },
      ],
    };
  },
};
```

**Rewrite vs Redirect 차이**

- **Rewrite**: URL 변경 없이 내용만 다른 곳에서 가져옴 (사용자는 모름)
- **Redirect**: URL이 바뀜 (브라우저가 새로 요청, 308/307 등)

**i18n 설정**

```js
i18n: {
  locales: ['default', 'ko-KR', 'en-US', ...],
  defaultLocale: 'default',  // prefix 없는 URL의 locale
  localeDetection: false,    // Accept-Language 헤더 자동 감지 끔
},
```

`localeDetection: false`이므로 브라우저 언어에 따른 자동 리다이렉트 없음. 대신 middleware에서 쿠키 기반으로 직접 처리.

<br>

### <mark class="yellow">`_document.tsx` — 렌더링된 앱을 감싸는 HTML 셸</mark>

📎 [공식 문서 — Custom Document](https://nextjs.org/docs/pages/building-your-application/routing/custom-document)

<img width="665" height="149" alt="Image" src="https://github.com/user-attachments/assets/3501272d-d74a-40f6-9642-383e627e492e" />

<img width="704" height="140" alt="Image" src="https://github.com/user-attachments/assets/784c6235-5ba3-4c50-8f0c-986d03fb9557" />

> 커스텀 Document는 페이지 렌더링에 사용되는 `<html>`과 `<body>` 태그를 업데이트할 수 있습니다.  
> `_document`는 서버에서만 렌더링되므로 `onClick` 같은 이벤트 핸들러는 사용할 수 없습니다.  
> `_document`의 `getInitialProps`는 클라이언트 사이드 전환 시에는 호출되지 않습니다.

- **실행 시점**: 서버에서 HTML 생성 시 실행. **클라이언트 전환(네비게이션) 시에는 호출되지 않음**
- **역할**: `<html>`, `<body>` 바깥 껍데기 정의. 렌더링된 앱(React 트리)을 감싸는 HTML 셸 역할. 서버 전용 스크립트/태그 삽입

```tsx
import { Html, Head, Main, NextScript } from "next/document";

export default function Document() {
  return (
    <Html lang="en">
      <Head />
      <body>
        <Main /> {/* 실제 React 앱이 렌더링되는 위치 */}
        <NextScript /> {/* Next.js JS 번들 주입 */}
      </body>
    </Html>
  );
}
```

> `_document`에서는 React 이벤트 핸들러, `useState`, `useEffect` 사용 불가. `<Main />` 바깥에 있는 React 컴포넌트는 브라우저에서 초기화되지 않으므로 공유 컴포넌트(메뉴, 툴바 등)는 여기가 아닌 Layout에서 처리한다.

<br>

### <mark class="yellow">`_app.tsx` + getInitialProps (GIP) — 모든 페이지의 공통 래퍼</mark>

- **실행 시점**: 모든 페이지 요청마다 실행. SSR/GSSP 페이지는 서버에서, GSSP 없는 페이지의 클라이언트 네비게이션 시에는 클라이언트에서 실행
- **역할**: 전역 Provider, 공통 레이아웃, 모든 페이지에 공통 props 주입

**Component란?**

Next.js 라우터가 URL → 파일 매핑을 먼저 결정하고, `_app`은 그 결과로 결정된 컴포넌트를 `Component`로 받음. `_app`이 직접 URL을 파싱하는 게 아님.

```ts
// /about 접근 시     → Component = pages/about.tsx
// /products 접근 시  → Component = pages/products/index.tsx
```

<br>

**GIP의 특성**

📎 [공식 문서 — getInitialProps with App](https://nextjs.org/docs/pages/building-your-application/routing/custom-app#getinitialprops-with-app)

<img width="711" height="196" alt="Image" src="https://github.com/user-attachments/assets/a6c55731-be90-4d22-855f-fca6eb7cc80a" />

> App에서 getInitialProps를 사용하면 getStaticProps가 없는 페이지의 자동 정적 최적화(ASO)가 비활성화됩니다.  
> 이 패턴은 권장하지 않습니다. 대신 App Router를 점진적으로 도입하는 것을 고려하세요.

- `App.getInitialProps`를 정의하면 자동 정적 최적화(ASO) **비활성화** → `getStaticProps`가 없는 페이지들의 정적 최적화 혜택이 사라짐 (`getStaticProps`가 있는 페이지는 여전히 정적 생성됨)
- `Component.getInitialProps?.(ctx)` 호출로 페이지별 GIP도 수동으로 실행해줘야 함 (`?.`로 GIP 없는 페이지는 `undefined` 반환)

```tsx
import App, { AppContext, AppInitialProps, AppProps } from "next/app";

type AppOwnProps = { example: string };

export default function MyApp({
  Component,
  pageProps,
  example,
}: AppProps & AppOwnProps) {
  return (
    <>
      <p>Data: {example}</p>
      <Component {...pageProps} />
    </>
  );
}

MyApp.getInitialProps = async (
  context: AppContext,
): Promise<AppOwnProps & AppInitialProps> => {
  // App.getInitialProps를 반드시 호출해야 페이지별 GIP도 실행됨
  const ctx = await App.getInitialProps(context);
  return { ...ctx, example: "data" };
};
```

<br>

### <mark class="yellow">getServerSideProps (GSSP) — 요청마다 서버 실행</mark>

- **실행 시점**: 매 요청마다 서버에서 실행 (클라이언트 네비게이션 시에는 API 엔드포인트로 호출됨)
- **GIP와의 차이**: GSSP는 서버 전용 (브라우저에서 절대 실행 안 됨), GIP는 서버+클라이언트 양쪽 가능

```ts
import type { GetServerSideProps, InferGetServerSidePropsType } from 'next'

type Repo = {
  name: string
  stargazers_count: number
}

export const getServerSideProps = (async () => {
  // 외부 API에서 데이터 fetch
  const res = await fetch('https://api.github.com/repos/vercel/next.js')
  const repo: Repo = await res.json()
  // props를 통해 페이지 컴포넌트로 데이터 전달
  return { props: { repo } }
}) satisfies GetServerSideProps<{ repo: Repo }>

export default function Page({
  repo,
}: InferGetServerSidePropsType<typeof getServerSideProps>) {
  return (
    <main>
      <p>{repo.stargazers_count}</p>
    </main>
  )
}
```

**GSSP 반환값 3가지**

| 반환                                       | 결과        |
| ------------------------------------------ | ----------- |
| `{ props: { ... } }`                       | 정상 렌더링 |
| `{ redirect: { destination, permanent } }` | 리다이렉트  |
| `{ notFound: true }`                       | 404 페이지  |

<br>
<br>

## <mark class="pink">GIP vs GSSP 비교</mark>

| 항목           | getInitialProps                                    | getServerSideProps    |
| -------------- | -------------------------------------------------- | --------------------- |
| 도입 시기      | Next.js 초기                                       | Next.js 9.3           |
| 실행 위치      | 서버 + 클라이언트 (GSSP 페이지 이동 시에는 서버만) | 서버만                |
| 사용 위치      | `_app.tsx`, 페이지                                 | 페이지만              |
| req/res 접근   | 클라이언트에서는 undefined                         | 항상 가능             |
| Tree shaking   | 불가 (클라이언트 번들에 포함)                      | 가능 (서버 코드 분리) |
| 정적 최적화    | `_app`에 쓰면 전체 비활성화                        | 해당 페이지만 SSR     |
| `_app.js` 사용 | 가능                                               | 불가                  |
| 추천 여부      | 레거시 (신규엔 GSSP 권장)                          | 현재 표준             |

<br>
<br>

## <mark class="pink">GIP vs GSSP 실행 타이밍 상세</mark>

**첫 페이지 로드 (SSR)**

```
서버: next.config (headers) → next.config (redirects) → Middleware → next.config (beforeFiles) → Filesystem/Dynamic Routes → next.config (afterFiles/fallback)
   → GSSP → _app.GIP → _document 렌더 → HTML 전송
브라우저: Hydration
```

<br>

**클라이언트 네비게이션 (next/link, router.push)**

📎 [공식 문서 — getInitialProps](https://nextjs.org/docs/pages/api-reference/functions/get-initial-props)

<img width="707" height="229" alt="Image" src="https://github.com/user-attachments/assets/6d06033e-a5a0-4c90-a006-2553764324a0" />

> 커스텀 `_app.js`에서 `getInitialProps`를 사용하고, 이동 대상 페이지가 `getServerSideProps`를 사용하는 경우, `getInitialProps`는 서버에서만 실행됩니다.

<br>

GSSP 페이지로 이동 시

```
브라우저: /_next/data/.../page.json 서버 요청
서버: GSSP + _app.GIP 서버에서 실행 → JSON 반환
브라우저: JSON 수신 후 페이지 렌더링 (클라이언트에서 GIP 재실행 없음)
```

GIP만 있는 페이지(GSSP 없음)로 이동 시

```
브라우저: _app.GIP 클라이언트에서 직접 실행
         → ctx.req가 undefined이므로 ctx.req?.headers처럼 방어 코드 필요
```

<br>

즉, **GSSP가 있는 페이지로 이동할 때는 `_app.GIP`도 서버에서만 실행**된다. `ctx.req`가 undefined일 상황을 고려해야 하는 경우는 GSSP 없이 GIP만 사용하는 페이지로 이동할 때다.

<br>
<br>

## <mark class="pink">전체 아키텍처 요약</mark>

### 앱 라우팅 구성 (각 단계별 역할)

```
① next.config.mjs - headers
  - /api/*  → Cache-Control: no-store (캐시 비활성화)
  - /*      → X-Frame-Options: SAMEORIGIN (클릭재킹 방지)

② next.config.mjs - redirects
  - /old-blog/:slug → /blog/:slug (301, SEO 이전)
  - /legacy/*       → /new/* (308, 영구 이전)

③ Middleware (src/middleware.ts)
  - /dashboard/* 비로그인 → /login 리다이렉트
  - /* → x-user-id 헤더 주입 후 next()

④ beforeFiles rewrites
  - /some-page?overrideMe=1 → /somewhere-else (파일 있어도 우선 적용)

⑤ Filesystem 라우트
  - public/favicon.ico, _next/static/**, pages/index.tsx 등 정적 파일 처리

⑥ afterFiles rewrites
  - /non-existent → /somewhere-else (정적 파일 없을 때 적용)

⑦ Dynamic Routes
  - pages/[category]/index.tsx  → /:category
  - pages/blog/[slug].tsx       → /blog/:slug

⑧ fallback rewrites
  - /:path* → https://my-old-site.com/:path* (모든 매칭 실패 시 외부 폴백)
```

### 렌더링 파이프라인 (요청: /electronics)

```
⑦ Dynamic Routes에서 pages/[category]/index.tsx 매칭
  │
  ▼
getServerSideProps:
  - 외부 API에서 카테고리 데이터 fetch
  - { props: { repo } } 반환
  │
  ▼
_app.getInitialProps:
  - App.getInitialProps(context) 호출 → 페이지별 GIP 실행
  - 공통 데이터(예: example) 반환
  │
  ▼
_document.tsx:
  - <Html>, <Head />, <Main />, <NextScript /> 렌더링 (서버 전용)
  │
  ▼
_app.tsx 렌더:
  MyApp → <Component {...pageProps} />
  │
  ▼
클라이언트 Hydration
```
