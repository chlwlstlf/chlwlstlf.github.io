---
layout: single
title: "[우테코]Level3 styled-components 반응형 모듈화"
categories: woowacourse
toc: true
toc_sticky: true
---

# styled-components 반응형 유틸리티 함수

## <mark class="pink">📌Breakpoints</mark>

- 디바이스의 해상도 사이즈는 변동 가능성이 크므로, 원치 않는 결과를 초래할 수 있습니다.
- 따라서 ‘small’, ‘medium’, ‘large’와 같이 더 명확한 이름을 사용하는 것이 좋습니다.

```tsx
//bad
export const breakpoints: Record<Breakpoints, string> = {
  mobile: "@media (max-width: 639px)",
  tablet: "@media (max-width: 1047px)",
  desktop: "@media (min-width: 1048px)",
};

// good
export const breakpoints: Record<Breakpoints, string> = {
  small: "@media (max-width: 639px)",
  medium: "@media (max-width: 1047px)",
  large: "@media (min-width: 1048px)",
};
```

<br>
<br>

## <mark class="pink">📌Media Queries</mark>

**styles > media.ts**

```tsx
import { type CSSObject, type Interpolation, css } from "styled-components";

export type Breakpoints = "small" | "medium" | "large";

export const breakpoints: Record<Breakpoints, string> = {
  small: "@media (max-width: 639px)",
  medium: "@media (max-width: 1047px)",
  large: "@media (min-width: 1048px)",
};

const media = Object.entries(breakpoints).reduce((acc, [key, value]) => {
  acc[key as Breakpoints] = (
    first: CSSObject | TemplateStringsArray,
    ...interpolations: Interpolation<object>[]
  ) => css`
    ${value} {
      ${css(first, ...interpolations)}
    }
  `;
  return acc;
}, {} as Record<Breakpoints, (first: CSSObject | TemplateStringsArray, ...interpolations: Interpolation<object>[]) => ReturnType<typeof css>>);

export default media;
```

<br>
<br>

## <mark class="pink">📌사용 예시</mark>

```tsx
import styled from "styled-components";
import media from "styles/media";

function Home() {
  return <Form>{/* something code ... */}</Form>;
}

export default Home;

const Form = styled.form`
  width: 1200px;
  min-width: 1200px;
  padding-block: 180px;

  ${media.small`
    width: 86.6666vw;
    min-width: initial;
    padding-block: 33.3333vw 27.7777vw;
  `};
`;
```

<br>
<br>

참고

[https://medium.com/@duchanjo/styled-components로-반응형-작업하기-32a41d3966eb](https://medium.com/@duchanjo/styled-components%EB%A1%9C-%EB%B0%98%EC%9D%91%ED%98%95-%EC%9E%91%EC%97%85%ED%95%98%EA%B8%B0-32a41d3966eb)
