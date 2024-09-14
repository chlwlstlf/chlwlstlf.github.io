---
layout: single
title: "[우테코]Level4 필요한 것만 요청하기"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션1 - 필요한 것만 필요한 때에 요청하기

## <mark style='background-color: #ffdce0'>🔥2 필요한 것만 필요한 때에 요청하기</mark>

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>Problem: 어떤 타입의 요청이 큰가?</div>
    <div>Problem: 요청 우선순위 조정이 필요해</div>
  </p>
</div>

> 요청은 하는데 쓰이는 데가 없는데?

• 불필요한 리소스 요청이 있는 지 점검하기

• Tree shaking

> 이러 지금 보이는 화면에선 필요 없는데?

• dynamic import

• lazy loading & intersectionObserver

• (React) code splittting w/React.lazy, Suspense

> 이미지는 한 번만 가져오면 안 되나?

• image sprite

• 다른 방법으로 이미지 대체하기

> 이건 바로 필요해, 미리 가져와야 할 거 같은데?

• preload (+ preconnect, prefetch)

```html
<link
  rel="preload"
  fetchpriority="high"
  href="/static/hero_medium.webp"
  as="image"
  type="image/webp"
  imagesrcset="/static/hero_small.webp 500w, /static/hero_medium.webp 1500w, /static/hero_large.webp 2500w"
/>
```

> 이건 바로는 필요 없어 • defer, async - 페이지별 리소스 분리 - Tree
> shaking: 실제로 사용되지 않는데 쓸데없이 빌드 결과물에 포함되는 코드 제거하기 •
> (React) code splittting w/React.lazy, Suspense • dynamic import • lazy loading &
> intersectionObserver

```

```
