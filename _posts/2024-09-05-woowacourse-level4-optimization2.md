---
layout: single
title: "[우테코]Level4 1 요청 크기 줄이기"
categories: woowacourse
toc: true
toc_sticky: true
---

# 성능 개선 미션 - 소스코드, 이미지 크기 줄이기

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>Problem: 어떤 타입의 요청이 큰가?</div>
  </p>
</div>

<br>
<br>

## <mark class="pink">🔥소스코드 크기 줄이기</mark>

> 크기 줄일 방법들 다 적용했나?

<br>

**<mark style='background-color: #fff5b1'>압축(Minify)</mark>**

압축은 전체 소스코드 중 아래와 같은 경우를 제거하는 작업을 말한다.

- 불필요한 줄바꿈, 공백 밑 들여쓰기
- 짧게 쓸 수 있는 긴 구문(줄일 수 있는 if 구문, 형 변환 축약 등)
- 스코프 내 사용하지 않는 변수
- 주석
- 경우에 따라, console.log, debugger 등의 디버깅용 구문 또는 메서드 호출
- 경우에 따라, 무의미한 메서드 호출 및 루프(Google Closure Compiler 사용하여 적용)

<br>

**<mark style='background-color: #fff5b1'>난독화(Uglify)</mark>**

- 자바스크립트코드 자체를 분석하기 어렵게 만드는 과정
- 난독화를 했다고 보안처리 없이 중요 정보나 루틴을 자바스크립트에 넣는 것은 매우 위험
- 변수명, 함수명 치환에서부터 자바스크립트의 일부 루틴을 문자열로 바꿔 변수에 담고 뒤섞는 단계 등 여러 단계 존재
- 난독화의 단계를 높일 수록 루틴을 알아보기 어렵게 만들 수 있음
- 변수, 함수명 등이 줄어 용량이 감소하지만 난독화 단계를 높일수록 코드를 해석하고 실행하는 속도가 느려질 수 있으므로, 프로젝트에 맞게 선택하여 적용하는 것이 좋음

<br>

### <mark style='background-color: #fff5b1'>JS 압축</mark>

webpack 4부터 production 환경에서 기본으로 js minify와 uglify를 해준다.

<br>

### <mark style='background-color: #fff5b1'>CSS 압축</mark>

자바스크립트와 마찬가지로 CSS에도 해석에 불필요한 공백이 존재한다.

CSS in JS는 babel transpile 과정에서 minify한다. 하지만 CSS는 직접 제거해야 한다.

이를 제거하기 위해서 Webpack 공식 문서에서 제시하는 방식은 [CssMinimizerPlugin](https://webpack.js.org/plugins/css-minimizer-webpack-plugin/)을 활용하는 방식이다. 추가적으로 CSS를 별개의 파일로 분리하기 위해서 [MiniCssExtractPlugin](https://webpack.js.org/plugins/mini-css-extract-plugin/)을 활용하는 설정을 추천한다.

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <b>use: [MiniCssExtractPlugin.loader, "css-loader"]</b>
    <div>ts-loader와 MiniCssExtractPlugin.loader는 충돌이 나기 때문에 MiniCssExtractPlugin.loader만 남겨두었다.</div>
    <div>ts-loader를 제거한 이유는 이 규칙이 CSS 파일만 처리하기 때문이다.</div>
  </p>
</div>

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <b>minimizer: [`...`, new CssMinimizerPlugin()]</b>
    <div>Webpack 5에서는 기본적으로 TerserPlugin(자바스크립트 파일을 최소화하는 플러그인)이 이미 포함되어 있다.</div>
    <div>minimizer 옵션을 사용하면 기본 설정을 덮어쓴다. 즉, 만약 minimizer 배열에 아무것도 추가하지 않으면 Webpack이 기본 제공하는 TerserPlugin도 비활성화된다.</div>
    <div>...(spread 연산자)는 Webpack의 기본 minimizer 설정을 확장하는 역할을 한다. 즉, 기본 제공되는 TerserPlugin을 유지하면서, 추가적으로 CssMinimizerPlugin을 사용할 수 있게 한다.</div>
  </p>
</div>

```bash
npm i mini-css-extract-plugin
npm i css-minimizer-webpack-plugin
```

<br>

```tsx
···
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const CssMinimizerPlugin = require("css-minimizer-webpack-plugin");

module.exports = {
  ···,
  plugins: [
    ···,
    new MiniCssExtractPlugin({ filename: "[name].[contenthash].css" }),
  ],
  module: {
    rules: [
      ···,
      {
        test: /\.css$/i,
        use: [MiniCssExtractPlugin.loader, "css-loader"], //ts-loader 제거
      },
      ···,
    ],
  },
  optimization: {
    minimizer: [`...`, new CssMinimizerPlugin()], //minimizer: true
  },
};
```

### <mark style='background-color: #fff5b1'>소스코드 압축</mark>

cloudfront에서 압축할 수 있다.

자동으로 객체 압축 > Yes

![7](https://github.com/user-attachments/assets/e9e930af-cf2f-41ed-a246-2c696dad74e5)

<br>
<br>

## <mark class="pink">🔥이미지 크기 줄이기</mark>

> 이미지 해상도가 화면에 보이는 크기보다 크네  
> 해상도는 잘 맞췄는데, 그래도 용량이 크네  
> PC에선 괜찮았는데, 모바일에선 이렇게까지 큰 거 필요 없는데?

### <mark style='background-color: #fff5b1'>png 크기 줄이기</mark>

• PNG

- 무손실 포맷
- JPEG에 비해 5~10배까지도 커질 수 있음
- PNG-8, PNG-24 에 따라 용량 차이

• JPEG

- 손실 압축 방식
- 상대적으로 작은 크기
- +) Progressive JPEG

• WebP

- 손실 / 무손실 압축 모두 지원
- 손실 압축에서 JPEG보다 25~34%, 무손실 압축에서 PNG보다 26% 작은 크기
- 브라우저 지원 범위 확인 필요

<br>

1\. png → webp

이미지 크기를 줄이기 위해 우선 같은 해상도일 때 png보다 26% 작은 크기로 줄일 수 있는 webp로 변환하였다. 아래 사이트를 이용하였고, 크기를 실시간으로 파악할 수 있어 용이했다. quality가 같으면 120kb 아래로 낮아지지 않아서 35%로 변환하였다.

[이미지 변환 사이트 squoosh](https://squoosh.app/)

<br>

2\. png → jpg

webp는 Internet Explorer, Safari 13 이하, iOS Safari 13 이하는 지원하지 않는다. 이때는 png나 jpg로 보여주어야 하는데 jpg가 png보다 크기가 작기 때문에 jpg로 변환하였다. 그림판에서 png를 불러온 후 jpg로 저장하면 된다.

<br>

3\. Home.tsx에 적용

[[참고] source 태그 MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/source)

**`<picture>` 태그**

- 브라우저에게 여러 이미지 소스를 제공하고, 그 중 가장 적합한 이미지를 선택하게 한다.
- 주로 반응형 이미지나 포맷에 따른 대체 이미지를 제공하기 위해 사용된다. 예를 들어, WebP 같은 포맷을 지원하는 브라우저는 WebP 이미지를 로드하고, 지원하지 않는 브라우저는 JPG, PNG 등 다른 포맷을 로드한다.
- `<picture>` 태그 안에는 여러 개의 `<source>` 태그를 넣을 수 있으며, 이 태그들은 브라우저가 이미지 파일을 선택할 때 고려하는 우선순위에 따라 적용된다.

<br>

**`<source>` 태그**

- `<picture>` 태그 안에서 특정 조건에 맞는 이미지 소스를 지정한다. 브라우저가 조건에 맞으면 `<source>`에 정의된 이미지를 사용하고, 조건에 맞지 않으면 다음 이미지로 넘어간다.
- srcSet: 이미지의 경로를 지정한다. 여기에서는 WebP 형식의 이미지를 로드할 수 있게 설정되어 있다.
- type: 이미지의 포맷을 지정한다. 여기서는 WebP 포맷이 지정되어 있어, WebP를 지원하는 브라우저는 이 이미지를 선택한다.

```tsx
import heroImage from "../../assets/images/hero.jpg";
import heroImageWebp from "../../assets/images/hero.webp";

return (
  <picture>
    <source
      className={styles.heroImage}
      srcSet={heroImageWebp}
      type="image/webp"
    />
    <img className={styles.heroImage} src={heroImage} alt="hero image" />
  </picture>
);
```

<br>

4\. 결과 확인하기 Network > Img

png, 10.7mb → webp, 111kb

![8](https://github.com/user-attachments/assets/cf36faf8-1941-4080-af09-418c4ef29311)

<br>
<br>

### <mark style='background-color: #fff5b1'>gif 크기 줄이기</mark>

• GIF

- 무손실 포맷
- 대부분 매우 큰 용량
- 애니메이션, 투명도 지원

• WebM

- 비디오 및 오디오 파일 포맷
- 상대적으로 작음, 고효율 압축 알고리즘을 사용하여 적은 파일 크기로 고화질 비디오를 지원
- 애니메이션, 투명도 지원
- 브라우저 지원 범위 확인 필요

• MP4

- 비디오, 오디오 파일 포맷
- 효율적인 압축을 통해 상대적으로 작음
- 투명도 지원X
- 거의 모든 브라우저 및 디바이스에서 폭넓게 지원

<br>

1\. WebM과 MP4로 변환

WebM를 지원하지 않는 곳에서는 MP4를 보여 줘야 하기 때문에 둘다 변경하였다.

<br>

2\. Home.tsx

```tsx
<FeatureItem title="See trending gif" webmSrc={trendingWebm} mp4Src={trendingMp4} />
<FeatureItem title="Find gif for free" webmSrc={findWebm} mp4Src={findMp4} />
<FeatureItem title="Free for everyone" webmSrc={freeWebm} mp4Src={freeMp4} />
```

<br>

3\. FeatureItem.tsx

```tsx
import styles from "./FeatureItem.module.css";

type FeatureItemProps = {
  title: string;
  webmSrc: string;
  mp4Src: string;
};

const FeatureItem = ({ title, webmSrc, mp4Src }: FeatureItemProps) => {
  return (
    <div className={styles.featureItem}>
      <video className={styles.featureImage} autoPlay loop muted playsInline>
        <source src={webmSrc} type="video/webm" />
        <source src={mp4Src} type="video/mp4" />
      </video>
      <div className={styles.featureTitleBg}></div>
      <h4 className={styles.featureTitle}>{title}</h4>
    </div>
  );
};

export default FeatureItem;
```

<br>

4\. 결과 확인하기 Network > Media

webm과 mp4는 Img가 아니라 Media이다.

trending: 1.3MB → 563KB  
find: 2.0MB → 1.1MB  
free: 1.7MB → 541KB

![10](https://github.com/user-attachments/assets/d650fe09-1cf1-4aac-937e-e22c38fd1048)

<br>

참고

[https://mingule.tistory.com/66](https://mingule.tistory.com/66)
