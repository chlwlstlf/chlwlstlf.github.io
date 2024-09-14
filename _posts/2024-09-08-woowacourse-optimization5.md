---
layout: single
title: "[우테코]Level4 최소한의 변경만 일으키기"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션1 - 리렌더링 적게 일으키기

## <mark style='background-color: #ffdce0'>🔥4 최소한의 변경만 일으키기</mark>

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
