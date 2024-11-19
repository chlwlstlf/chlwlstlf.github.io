---
layout: single
title: "[우테코]Level4 4 최소한의 변경만 일으키기"
categories: woowacourse
toc: true
toc_sticky: true
---

# 성능 개선 미션 - 리렌더링 적게 일으키기

## <mark class="pink">🔥최소한의 변경만 일으키기</mark>

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>Problem: Main Tread 일이 왜 많은데?</div>
  </p>
</div>

> reflow, repaint가 생각한 것보다 많이 발생하는데?
> 똑같은 작업인데 매번 새로 해야 하나?
> 일 좀 나눠서 합시다

- 검색 결과를 불러올 때, 새로 추가된 요소들만 렌더

- LayoutShift 없이 hover 애니메이션이 일어나야 한다

- Frame Drop이 일어나지 않아야 한다.

<br>
<br>

## <mark class="pink">🔥새로 추가된 요소들만 렌더</mark>

검색 결과 > 추가 로드시 추가되는 결과에 대해서만 화면 업데이트가 새로 일어나야 한다. React DevTools의 Profiler 기준으로 기존에 있던 아이템이 다시 렌더되지 않는지 확인한다.

<br>

**<mark style='background-color: #fff5b1'>개선 전 결과</mark>**

모든 곳에 색이 칠해져 있으며 모두 렌더링이 반복되는 것을 알 수 있다.

![1000](https://github.com/user-attachments/assets/0dbe073c-b42d-4961-9f36-9e7c772021c0)

![1001](https://github.com/user-attachments/assets/e06281cd-2896-48ee-8995-213b9494ace5)

<br>

**<mark style='background-color: #fff5b1'>React.memo로 해결</mark>**

React.memo는 React에서 컴포넌트의 불필요한 리렌더링을 방지하기 위한 **고차 컴포넌트(Higher Order Component)**이다. props가 변경되지 않는 한 컴포넌트를 리렌더링하지 않도록 메모이제이션을 적용하여 성능을 최적화한다.

---

GifItem.tsx

```tsx
import React from "react";
import { GifImageModel } from "../../../../models/image/gifImage";

import styles from "./GifItem.module.css";

type GifItemProps = Omit<GifImageModel, "id">;

const GifItem = React.memo(({ imageUrl = "", title = "" }: GifItemProps) => {
  return (
    <div className={styles.gifItem}>
      <img className={styles.gifImage} src={imageUrl} />
      <div className={styles.gifTitleContainer}>
        <div className={styles.gifTitleBg}></div>
        <h4 className={styles.gifTitle}>{title}</h4>
      </div>
    </div>
  );
});

export default GifItem;
```

<br>

**<mark style='background-color: #fff5b1'>개선 후 결과</mark>**

빗금 되어 있는 부분이 memo 되어서 리렌더링이 일어나지 않는 부분이다.

![1002](https://github.com/user-attachments/assets/b429d7ba-bd99-42a2-8249-0fdafb1cb9b8)

![1003](https://github.com/user-attachments/assets/ca295719-f71f-4907-8646-2cb5e872c4a2)

<br>
<br>

## <mark class="pink">🔥Layout Shift 없애기</mark>

Layout Shift 없이 애니메이션이 일어나야 한다. (대상) CustomCursor, 검색 결과 > hover, 도움말 패널 열고닫기 애니메이션

<br>

**<mark style='background-color: #fff5b1'>Layout Shift 해결 방법</mark>**

애니메이션이 레이아웃 시프트(Layout Shift) 없이 발생하도록 하려면, 레이아웃 재계산(레이플로우)를 최소화하고 **transform, translate**과 **opacity**를 활용하는 것이 중요하다. 이러한 속성은 레이아웃 재계산을 발생시키지 않기 때문에 레이아웃 시프트 없이 부드러운 애니메이션을 구현할 수 있다.

렌더링 엔진이 요소의 배치와 크기를 다시 계산하지 않도록 하고, 레이어를 유지한 채로 이동하기 때문에 레이아웃이 변경되지 않는다.

브라우저의 레이아웃 엔진(layout engine)이 아니라 **컴포지팅(compositing)** 엔진에서 처리되기 때문에 레이아웃을 변경하지 않고도 요소의 위치를 조정할 수 있다.

<br>

**<mark style='background-color: #fff5b1'>1. CustomCursor.tsx</mark>**

CustomCursor.tsx 최적화 전

```tsx
useEffect(() => {
  if (cursorRef.current) {
    cursorRef.current.style.top = `${mousePosition.pageY}px`;
    cursorRef.current.style.left = `${mousePosition.pageX}px`;
  }
}, [mousePosition]);
```

![1004](https://github.com/user-attachments/assets/9bcec1be-618b-466e-acf4-2dc61d2d2092)

<br>

CustomCursor.tsx 최적화 후: Layout Shifts가 아예 사라졌다

```tsx
useEffect(() => {
  if (cursorRef.current) {
    cursorRef.current.style.transform = `translate(${mousePosition.pageX}px, ${mousePosition.pageY}px)`;
  }
}, [mousePosition]);
```

![1005](https://github.com/user-attachments/assets/b0b35d7c-1a91-4b9a-aa75-7fdf80c34ed8)

<br>

**<mark style='background-color: #fff5b1'>2. search 페이지 > hover</mark>**

GifItem.module.css 최적화 전

```css
.gifItem {
  position: relative;
  width: 280px;
  height: 280px;
  background: radial-gradient(
    110.23% 110.23% at 50% 50%,
    #bd00ff 0%,
    rgba(233, 97, 195, 0.64248) 61.18%,
    rgba(232, 109, 198, 0.1) 97.71%
  );
  overflow: hidden;
  transition: all 0.2s ease-in;
  border-radius: 4%;
}

.gifItem:hover {
  top: -0.75rem;
}
```

![1006](https://github.com/user-attachments/assets/ef5477ba-21d7-4361-92c7-4638455b9a8c)

<br>

GifItem.module.css 최적화 후

```css
.gifItem {
  position: relative;
  width: 280px;
  height: 280px;
  background: radial-gradient(
    110.23% 110.23% at 50% 50%,
    #bd00ff 0%,
    rgba(233, 97, 195, 0.64248) 61.18%,
    rgba(232, 109, 198, 0.1) 97.71%
  );
  overflow: hidden;
  transition: transform 0.2s ease-in; //변경
  border-radius: 4%;
}

.gifItem:hover {
  transform: translateY(-0.75rem); //변경
}
```

![1007](https://github.com/user-attachments/assets/431b34df-cef2-4e66-8551-7f2c5be84096)

<br>

**<mark style='background-color: #fff5b1'>3. search 페이지 > 도움말 패널 열고닫기 애니메이션</mark>**

HelpPanel.module.css 최적화 전

```css
.selectedItemContainer {
  position: fixed;
  right: -320px;
  top: 0;
  width: 320px;
  height: 100%;
  z-index: 10;
  background: rgba(255, 255, 255, 0.9);
  box-shadow: 0 4px 30px rgba(0, 0, 0, 0.1);
  backdrop-filter: blur(5px);
  -webkit-backdrop-filter: blur(5px);
  opacity: 0;
  transition: 0.5s all cubic-bezier(0.82, 0.085, 0.395, 0.895);
}

.selectedItemContainer.showSheet {
  right: 0;
  opacity: 1;
}
```

![1008](https://github.com/user-attachments/assets/42947499-cdbd-4304-84df-6e536843112f)

<br>

HelpPanel.module.css 최적화 후

```css
.selectedItemContainer {
  position: fixed;
  right: 0; /* right: 0으로 고정 */
  top: 0;
  width: 320px;
  height: 100%;
  z-index: 10;
  background: rgba(255, 255, 255, 0.9);
  box-shadow: 0 4px 30px rgba(0, 0, 0, 0.1);
  backdrop-filter: blur(5px);
  -webkit-backdrop-filter: blur(5px);
  opacity: 0;
  transform: translateX(100%); /* 화면 밖으로 이동 */
  transition: transform 0.5s cubic-bezier(0.82, 0.085, 0.395, 0.895), opacity
      0.5s ease;
}

.selectedItemContainer.showSheet {
  transform: translateX(0); /* 슬라이드로 화면 안으로 이동 */
  opacity: 1;
}
```

![1009](https://github.com/user-attachments/assets/98f142c4-3231-4c67-a73b-26cfd6569a43)

<br>
<br>

## <mark class="pink">🔥Frame Drop 없애기</mark>

Frame Drop이 일어나지 않아야 한다. (Chrome DevTools 기준) Partially Presented Frame 역시 최소로 발생해야 한다. (대상) 메인 페이지의 CustomCursor, 스크롤 애니메이션

Frame drop은 애니메이션이나 동영상 재생 등에서 발생하는 현상이다.

화면에 표시되는 연속적인 프레임이 일시적으로 누락되는 것인데 이로 인해 버벅거림이 발생할 수 있다.

<br>

**<mark style='background-color: #fff5b1'>1. CustomCursor</mark>**

빨간색이 없으므로 Partially Presented Frame가 일어나지 않고 있다.

![1010](https://github.com/user-attachments/assets/4ba72886-2d0d-427d-8712-0bad30e1a26a)

<br>

**<mark style='background-color: #fff5b1'>2. 스크롤 애니메이션</mark>**

빨간색이 없으므로 Partially Presented Frame가 일어나지 않고 있다

![1011](https://github.com/user-attachments/assets/77a14371-eba4-41b9-baa8-9816577c8dcd)
