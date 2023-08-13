---
layout: archive
title: "MegaBox 2022"
categories: js
toc: true
toc_sticky: true
---

# MegaBox 사이트 클론

## <mark style='background-color: #ffdce0'>헤더 만들기</mark>

2022-04-13

- 가운데 MegaBox 로고 넣기
- util 정보 넣기 (오른쪽 왼쪽 나눠서)
- link 정보 넣기 (오른쪽 왼쪽 나눠서)
- nav 만들기
- 부모 리스트와 자식 리스트를 나눠서 코드를 짠 후 자식 리스트는 마우스를 hover해야 보이게 하기

[https://chlwlstlf.github.io/megabox2022/index01.html](https://chlwlstlf.github.io/megabox2022/index01.html)

![megabox1](https://user-images.githubusercontent.com/63334368/162913022-0913d343-a796-4a9d-8294-9d7f3ce9c0b2.png)

:bulb:문제해결

1. padding 값이 자꾸 살아나서 padding-left : 0; 으로 해주었다.
2. display:none; 하고 마우스 올렸을 때 display:block 으로 바꾸고 싶었는데 선택자를 잘못 선택해서 3시간 동안 헤맸다.

:bookmark:아쉬운점

1. 글씨체를 바꾸고 싶은데 원래 사이트 글씨체랑 같게 했는데도 바뀌지 않는다.
2. 필요하지 않은 css를 지우고 싶은데 아직 개발자 도구 보는 실력이 낮은거 같다.
3. :after 와 :before 을 아직도 잘 모르겠다.

## <mark style='background-color: #ffdce0'>섹션01 만들기</mark>

2022-04-26

- 포스터 넣고 마우스 올리면 설명 뜨기
- 마우스 계속 움직이는 거 @keyframes와 animation으로 구현하기

[https://chlwlstlf.github.io/megabox2022/index02.html](https://chlwlstlf.github.io/megabox2022/index02.html)

![megabox2](https://user-images.githubusercontent.com/63334368/165307170-7e0ce7fd-f299-4367-827d-dbc251170fdf.png)

:bulb:문제해결

1. 이미지에 마우스 올렸을 때 설명 뜨는거는 nav에서 했던 거라 쉽게 할 수 있었다.
2. 마우스 아이콘이 계속 움직이는 걸 javascript에서 하려고 했는데 안 돼서 css에서 구현했다.
3. z-index 때문인지 nav가 가려졌는데 nav의 z-index 값을 적당히 큰 값으로 하여 위에 보이게 했다.

:bookmark:아쉬운점

1. 배경 포스터 위에 투명도가 0.8인 검정 화면을 덮을려고 했는데 안 된다.
2. 헤더부터 div로 묶고 싶은데 안 묶어진다.
