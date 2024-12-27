---
layout: single
title: "[Next.js]Next.js 시작하기"
categories: next
toc: true
toc_sticky: true
---

# 2022 코딩애플 리액트 강의

## <mark class="pink">📂설치</mark>

1\. v.18 이상의 node.js, vscode 설치

<br>

2\. next 설치

```bash
npx create-next-app@14
```

![1](https://github.com/user-attachments/assets/6509ebb4-b73f-4c70-9397-82998215f354)

<br>

3\. 버전 업데이트가 되어서 아래 명령어도 추가적으로 해주었다.

```bash
npm i -g create-next-app
```

<br>
<br>

## <mark class="pink">📂시작</mark>

1\. vscode에서 폴더 열기

2\. 터미널에서 `npm run dev` 한 후 `localhost:3000`을 실행하면 된다.

<br>
<br>

## <mark class="pink">📂폴더 설명</mark>

|      폴더명       |                        설명                         |
| :---------------: | :-------------------------------------------------: |
|     app 폴더      |                  내가 코드 짤 폴더                  |
|     page.tsx      |                     메인 페이지                     |
|    layout.tsx     | 메인 페이지 감싸는 용도의 페이지(head 태그, 상단바) |
|      public       |          이미지나 static 파일 모아놓는 곳           |
|     api 폴더      |                 서버 기능 만드는 곳                 |
|  next.config.mjs  |                  nextjs 설정 파일                   |
| node_modules 폴더 |            설치한 라이브러리 보관용 폴더            |
|   package.json    |         설치한 라이브러리 버전 기록용 파일          |

<br>
<br>

## <mark class="pink">📂라우팅</mark>

**<mark class="yellow">라우팅</mark>**

1\. `/list` url을 만들고 싶으면 `list`로 폴더를 만든 후 `page.tsx`에 코드를 작성하면 된다.

![2](https://github.com/user-attachments/assets/68dd7c21-6eb6-4afb-8252-99da8f507ccf)

<br>

2\. 다른 곳에선 아래처럼 list 페이지로 갈 수 있다.

```tsx
<Link href="/list">list페이지</Link>
```

<br>

**<mark class="yellow">layout.tsx</mark>**

1\. `layout.tsx` 파일에는 공통으로 보여준 UI를 넣는다. (메타데이터, 헤더, 푸터)

2\. 그 이유는 `children` 인자로 페이지를 받고 있기 때문이다.

```tsx
import type { Metadata } from "next";
import "./globals.css";
import Link from "next/link";

export const metadata: Metadata = {
  title: "Create Next App",
  description: "Generated by create next app",
};

export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  return (
    <html lang="en">
      <body>
        <div className="navbar">
          <Link href="/">home</Link>
          <Link href="/list">list페이지</Link>
        </div>
        {children}
      </body>
    </html>
  );
}
```

<br>
<br>

## <mark class="pink">📂Image 넣는 법</mark>

**<mark class="yellow">최적화된 이미지</mark>**

1\. Lazy Loading: 화면에 보이지 않는 이미지를 로드하지 않는다. (초기 페이지 로드 시간 단축)

2\. 사이즈 최적화: 사이즈에 따라 이미지를 최적화하고, webp, avif 등으로 포맷을 변환한다.

3\. Layout Shift 방지: 이미지 로드 중 레이아웃을 변경을 방지한다.

<br>

**<mark class="yellow">Image 컴포넌트</mark>**

next에서 제공해주는 `Image` 컴포넌트로 최적화된 이미지를 사용할 수 있다.

> 💡 주의. 외부 이미지를 넣을 떄 width랑 height를 꼭 지정해줘야 한다.

```tsx
import Image from "next/image";
import tomato from "public/food0.png";

export default function List() {
  return (
    <div>
      <h4 className="title">상품목록</h4>
      <Image src={tomato} className="food-img" alt="tomato" />
    </div>
  );
}
```

<br>
<br>

## <mark class="pink">📂server component vs client component</mark>

**<mark class="yellow">server component</mark>**

기본적으로 server component이다.  
pages.tsx, layout.tsx에 만드는 컴포넌트들은 전부 server component이다.

장점: 페이지 로드 시 자바스크립트가 필요 없어서 빠르다.  
단점: html 안에 자바스크립트를 못 넣는다. useState, useEffect, onClick 사용 불가

<br>

**<mark class="yellow">client component</mark>**

파일 맨 위에 `'use client'`라는 코드를 넣으면 그 파일은 client component가 된다.

장점: html 안에 자바스크립트를 넣어서 기능 개발을 할 수 있다.  
단점: 자바스크립트로 인해 페이지 용량이 커지고 페이지 로딩 속도도 느려질 수 있다.

특히 client component를 로드하려면 hydration이라는 과정을 거치게 되는데 이 때문에 페이지 로드 속도가 더 느려진다.

> hydration이란? 서버에서 받은 html에 자바스크립트를 붙이는 과정

<br>

**<mark class="yellow">어떻게 짜나?</mark>**

큰 페이지들은 보통 server component로 만들고 자바스크립트 기능이 필요한 특정 부분은 client component로 만들어서 넣는게 좋은 습관이다. 컴포넌트 분리가 핵심이다.