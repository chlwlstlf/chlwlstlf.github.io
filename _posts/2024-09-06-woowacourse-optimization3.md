---
layout: single
title: "[우테코]Level4 2 필요한 것만 요청하기"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션1 - 필요한 것만 필요한 때에 요청하기

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>Problem: 어떤 타입의 요청이 큰가?</div>
    <div>Problem: 요청 우선순위 조정이 필요해</div>
  </p>
</div>

<br>

## <mark style='background-color: #ffdce0'>🔥해당 스크립트 리소스만 가져오기</mark>

**<mark style='background-color: #fff5b1'>목표</mark>**

Home 페이지에서 불러오는 스크립트 리소스에 Search 페이지의 소스 코드가 포함되지 않아야 한다.

<br>

**<mark style='background-color: #fff5b1'>Code Splitting</mark>**

코드의 양이 너무 많아지는 경우, 하나의 번들 파일이 너무 커져, 그 파일을 불러오고 실행하는데 많은 시간이 소요될 수 있다. 그래서 코드 스플릿팅이 필요하다. 코드 스플릿팅의 장점은 반드시 필요한 코드만 불러오고 요청을 병렬적으로 처리해서 로딩 속도를 개선할 수 있다는 점이다.

현재 페이지에서 필요한 리소스만 받아올 수 있도록 React lazy와 suspense를 사용하였다. lazy를 사용하면 컴포넌트가 비동기로 로드되기 때문에 suspense가 필수라고 한다!

<br>

**<mark style='background-color: #fff5b1'>App.tsx</mark>**

```tsx
import { lazy, Suspense } from "react";
import { BrowserRouter as Router, Route, Routes } from "react-router-dom";

const Home = lazy(() => import("./pages/Home/Home"));
const Search = lazy(() => import("./pages/Search/Search"));

import NavBar from "./components/NavBar/NavBar";
import Footer from "./components/Footer/Footer";

import "./App.css";

const App = () => {
  return (
    <Router>
      <Suspense fallback={<div>로딩중!</div>}>
        <NavBar />
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/search" element={<Search />} />
        </Routes>
        <Footer />
      </Suspense>
    </Router>
  );
};

export default App;
```

<br>

**<mark style='background-color: #fff5b1'>Code Splitting 결과</mark>**

Home 페이지에서는 Home.tsx 만 불러오는 것을 볼 수 있다.

![11](https://github.com/user-attachments/assets/beb8110f-0704-41cf-877d-d745adac5c40)

<br>

그 후 Search 페이지로 넘어가면 Search.tsx 만 추가적으로 불러온다.

![12](https://github.com/user-attachments/assets/184f449f-0b95-458a-ad3b-6b186403be61)

<br>
<br>

## <mark style='background-color: #ffdce0'>🔥아이콘 패키지 Tree Shaking</mark>

**<mark style='background-color: #fff5b1'>목표</mark>**

`react-icons` 패키지에서 실제로 사용하는 아이콘 리소스만 빌드 결과에 포함되어야 한다.

<br>

**<mark style='background-color: #fff5b1'>webpack bundle analyzer</mark>**

번들 파일이 어떻게 구성되었는지 쉽게 파악하게 해주는 시각화 도구이다.

- 용량별로 시각화를 해주고, 어떤 라이브러리가 많이 사용되고 있는지를 알 수 있다.
- 사이드바를 이용해 검색과 필터링도 가능하다.
- 사용 중인 라이브러리의 디펜던시들도 찾을 수 있다. 덕분에 직접 사용하진 않지만 번들에 포함된 라이브러리들을 확인할 수 있다.

![13](https://github.com/user-attachments/assets/1d4db305-afb4-4472-8710-381f32b90321)

설치

```bash
npm i -D webpack-bundle-analyzer
```

webpack.config.js

```jsx
const BundleAnalyzerPlugin =
  require("webpack-bundle-analyzer").BundleAnalyzerPlugin;

module.exports = {
  plugins: [new BundleAnalyzerPlugin()],
};
```

<br>

**<mark style='background-color: #fff5b1'>Tree Shaking</mark>**

Tree shaking이란 사용되지 않는 코드를 제거하기 위해 JavaScript 컨텍스트에서 일반적으로 사용되는 용어이다. 우선 Tree Shaking은 CommonJS 모듈이 아닌 **ES Module** 방식에서 동작한다. 때문에 빌드되는 과정에서 ES Module로 변환되지 않도록 해야 한다.

참고로 webpack5부터는 기본으로 알아서 제거해준다. 그래도 설정을 직접해야한다 싶으면 `"sideEffects": false`를 추가하면 된다. 프로젝트 내의 모든 파일이 부수효과가 없다고 가정하는 설정이다. 이 경우 Webpack은 CSS 파일을 포함한 사용되지 않는 모든 파일을 Tree shaking을 통해 제거하려고 한다. 즉, CSS 파일이 명시적으로 사용되지 않으면 삭제될 위험이 있다.

따라서 `"sideEffects": ["*.css"]`라고 명시하여 CSS 파일을 트리 셰이킹에서 제외한다.
CSS 파일은 코드에서 직접적으로 사용하지 않더라도 전역적으로 스타일을 적용하기 때문에 부수효과를 가질 수 있다.

```json
{
  ···,
  "babel": {
    "presets": [
      "@babel/preset-env",
      "@babel/preset-react",
      {
        "modules": false
      }
    ],
    "sideEffects": [
      "*.css"
    ],
    "plugins": [
      "@babel/plugin-transform-runtime"
    ]
  }
}
```

<br>

**<mark style='background-color: #fff5b1'>Tree Shaking 확인하기</mark>**

Tree shaking은 주로 production 환경에서 작동하는 최적화 기법이다. 개발하는 도중 prod로 빌드 된 파일의 Tree shaking을 확인하고 싶다면 아래와 같이 명령하면 된다.

```bash
npm run build:prod && npx webpack-bundle-analyzer dist/stats.json
```

<br>

**<mark style='background-color: #fff5b1'>최적화 전 - react-icons</mark>**

react-icons 패키지에서 실제로 사용하는 아이콘 뿐만 아니라 모든 리소스가 빌드 결과에 포함된다. 아이콘을 3개만 import하고 있는데 stat size가 매우 큰 것을 볼 수 있다.

```tsx
import { AiOutlineInfo, AiOutlineClose } from "react-icons/ai";
```

![14](https://github.com/user-attachments/assets/f7f3bbbe-20b5-45cc-99bb-b8797e3ae24d)

<br>

**<mark style='background-color: #fff5b1'>최적화 후 - @react-icons/all-file</mark>**

@react-icons/all-files 라이브러리는 아이콘 별로 자바스크립트 파일을 별도로 가지고 있기 때문에, 빌드 시 트리쉐이킹 방식으로 더 적은 크기의 chunk를 만들 수 있다. stat size가 634.84kb에서 8.34kb로 줄어든 것을 볼 수 있다.

```tsx
import { AiOutlineInfo } from "@react-icons/all-files/ai/AiOutlineInfo";
import { AiOutlineClose } from "@react-icons/all-files/ai/AiOutlineClose";
```

![15](https://github.com/user-attachments/assets/7fa3a80f-0116-453c-8d26-ee4ce085e69c)

<br>
<br>

## <mark style='background-color: #ffdce0'>🔥이미지 미리 가져오기</mark>

**<mark style='background-color: #fff5b1'>[preload](https://web.dev/preload-critical-assets/)</mark>**

```html
<link rel="preload" href="/style.css" as="style" />
```

- Resource priority: High
- 현재 페이지에서 바로 필요한 리소스로, 빠르게 가져와야 한다는 것을 브라우저에게 알려주는 것
- 받아오되(fetch), 바로 실행(execute)은 하지 않음. (= 스크립트를 미리 받아와도 실행은 나중에)
- onload 이후 3초 이내에 preload한 리소스를 사용하지 않을 경우 크롬에서는 경고 로그
  받아온 뒤 브라우저에 캐시
- 사용 예시
  - 폰트, Critical Rendering Path에서 필요한 리소스, 즉, 초기 렌더링에 반드시 필요한 리소스

<br>

**<mark style='background-color: #fff5b1'>[prefetch](hhttps://web.dev/link-prefetch/)</mark>**

```html
<link rel="prefetch" href=“/next-chunk.js” as=“script" />
```

- Resource priority: Low
- 브라우저에게 미래에 필요할 수 있는 전체 페이지, 혹은 특정 리소스(script, css 등)를 미리 다운로드 받아두라고 알려주는 것
- 현재 페이지가 다 로드된 이후 후순위로 받아서 prefetch cache에 넣어둔다.
- 현재 페이지의 로드 시간에는 영향을 미치지 않지만, 다음 navigation의 FCP나 TTI에 영향을 미친다.
- 사용 예시
  - 같은 앱의 다른 페이지에서 사용되는 js 번들 / 현재 페이지에 쓰이더라도 초기 렌더링에선 필요 없는 리소스(ex. 모달)
  - 페이징 된 목록에서 다음 페이지의 컨텐츠

<br>

**<mark style='background-color: #fff5b1'>[preconnect](https://web.dev/preconnect-and-dns-prefetch/)</mark>**

```html
<link rel="preconnect" href=“https://example.com”/>
```

- 브라우저에서 서버와 연결만 미리 맺어두고 있어 달라고 알려주는 것
- 리소스 다운로드는 나중에 하더라도 connecting을 미리 하고 있는 것
- connection 미리 맺어두기로 개별 요청당 100-500ms 정도 로드 시간을 절약
- 사용 예시
  - 외부 도메인 리소스 (ex. 구글 폰트)

<br>

**<mark style='background-color: #fff5b1'>hero 사이즈 별로 preload하기</mark>**

[Fetch Priority API로 리소스 로드 최적화](https://web.dev/articles/fetch-priority?hl=ko)

hero 이미지는 화면이 로드되면서 바로 보여야 하는 이미지이고, webm 파일들은 스크롤을 내렸을 때 보이는 리소스들이다. 따라서 이미지는 preload 뿐만 아니라 `fetchpriority="high"`도 지정해 주었다. 기본 우선 순위는 `중간`이며 높이고 싶으면 `high`를 지정해주면 된다.

또 화면에 사이즈에 따라 로드되는 이미지를 정하고 싶다면 `imagesrcset` 속성을 활용하면 된다.

```html
<link
  rel="preload"
  fetchpriority="high"
  href="/static/hero_medium.webp"
  as="image"
  type="image/webp"
  imagesrcset="/static/hero_small.webp 500w, /static/hero_medium.webp 1500w, /static/hero_large.webp 2500w"
/>
<link rel="preload" href="/static/find.webm" as="video" type="video/webm" />
<link rel="preload" href="/static/free.webm" as="video" type="video/webm" />
<link rel="preload" href="/static/trending.webm" as="video" type="video/webm" />
```
