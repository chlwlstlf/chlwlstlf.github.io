---
layout: single
title: "[우테코]Level4 로딩, 렌더링 성능 개선이란?"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션1 - 성능 초깃값 측정

## <mark style='background-color: #ffdce0'>📌성능 개선 들어가기</mark>

> "성능이 곧 사용자 경험이다"

측정하기 → 분석하기 → 개선하기

프론트엔드 성능 = 로딩 성능(페이지가 얼마나 빨리 보이는 지) + 렌더링 성능(사용자 인터렉션에 얼마나 빠르게/효율적으로 반응하는 지)

<br>
<br>

## <mark style='background-color: #ffdce0'>📌개선 사항 체크</mark>

**<mark style='background-color: #fff5b1'>로딩 성능 개선</mark>**

1 요청 크기 줄이기

2 필요한 것만 필요한 때에 요청하기

3 같은 건 매번 새로 요청하지 않기

<br>

**<mark style='background-color: #fff5b1'>렌더링 성능 개선</mark>**

4 최소한의 변경만 일으키기

<br>
<br>

## <mark style='background-color: #ffdce0'>🔥로딩 성능 수치</mark>

**<mark style='background-color: #fff5b1'>배포 링크</mark>**

[https://chlwlstlf.github.io/perf-basecamp/](https://chlwlstlf.github.io/perf-basecamp/)

<br>

**<mark style='background-color: #fff5b1'>환경</mark>**

Desktop 기준 CPU 6x slowdown Network Slow 4G 환경

[그림1]

<br>

**<mark style='background-color: #fff5b1'>Lighthouse</mark>**

목표: Lighthouse 페이지 로드시 performance 점수 95점 이상

현재: 70점

[그림2]

[**Performance**](https://web.dev/performance-scoring/)는 화면에 콘텐츠가 얼마나 빨리 표시되고, 유저가 해당 콘텐츠를 얼마나 빨리 인식하는지에 대한 점수를 나타낸 것이다.

[**Accessibility**](https://web.dev/accessibility-scoring/)는 서비스의 접근성을 검사한다. `<img>` 태그에 alt 속성이 있는지, aria-\* 태그가 role과 잘 맞는지 등에 대해 검사한다.

[**Best Practice**](https://web.dev/lighthouse-best-practices/)에서는 웹에대한 표준 모범 사례를 잘 따르고 있는지 확인한다. console의 오류를 확인하는 등의 작업을 수행한다.

[**SEO**](https://developers.google.com/search/docs/advanced/guidelines/webmaster-guidelines)는 서비스가 검색 최적화되어있는지 확인한다. robots.txt 파일이 유효한지, `<title>` 태그가 적절하게 들어갔는지 등을 확인한다.

[**Progressive Web App**](https://web.dev/pwa-checklist/)은 점수화되어 나타나지 않는다. 서비스가 HTTP를 HTTPS로 redirect 하는지, 응답 코드는 잘 나타나는지 등을 확인해 합격/실패만을 나타낸다.

<br>

**<mark style='background-color: #fff5b1'>스크립트 리소스 크기</mark>**

목표: Home 페이지에서 불러오는 스크립트 리소스 크기 < 60kb

현재: 253kb

[그림3]

<br>

**<mark style='background-color: #fff5b1'>히어로 이미지 크기</mark>**

목표: 히어로 이미지 크기 < 120kb

현재: 10.7mb

[그림4]

<br>

**<mark style='background-color: #fff5b1'>WebPageTest</mark>**

[WebPageTest 사이트](https://www.webpagetest.org/)

환경: 프랑스 파리에서 Fast 3G 환경으로 접속했을 때

[그림6]

목표: Home 첫 번째 로드시 LCP < 2.5s, Home 두 번째 이후 로드시 LCP < 1.2s

현재: Home 첫 번째 로드시 LCP < 2.583s, Home 두 번째 이후 로드시 LCP < 1.225s

[현재 결과 페이지](https://www.webpagetest.org/result/240904_BiDcDA_242/)

[그림5]

<br>

**<mark style='background-color: #fff5b1'>추가적인 성능 개선</mark>**

• 화면 버벅임 최소화

• Dropped Frame 없음.

• Partially Presented Frame 최소화.
