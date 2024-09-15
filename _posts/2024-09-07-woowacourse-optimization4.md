---
layout: single
title: "[우테코]Level4 3 같은 건 매번 새로 요청하지 않기"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션1 - cache 사용하기

## <mark style='background-color: #ffdce0'>🔥3 같은 건 매번 새로 요청하지 않기</mark>

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>Problem: 같은 걸 매번 원본 서버에 요청</div>
  </p>
</div>

> 똑같은 리소스를 다시 요청하는 거라면, 캐시해서 응답 시간 줄이기

- CDN 적용 & 캐시 적용

• Cache-Control

• private, public 중 하나. 혹은 설정하지 않음 (기본값은 private)

• max-age, s-maxage, no-cache, no-store 중 하나

• 콤마로 구분해 여러 값 지정 가능

• 문제 상황 1) 서비스 신규 런칭을 앞두고 있습니다.

• 앞으로 꾸준히 버전 업데이트를 할 예정입니다.

• html

    • Cache-Control: no-cache (+ Etag/Last-Modified)

• css/js/image

    • Cache-Control: max-age=31536000

    • content hash 혹은 version 정보 경로에 포함

• 문제 상황 2) 서비스가 쭉쭉 성장해서 어느새 미국 진출을 앞두고 있습니다.

• CDN을 적용합니다.

• html

• Cache-Control: public, s-maxage=31536000, max-age=0 + invalidation

• css/js/image

• Cache-Control: max-age=31536000

• content hash 혹은 version 정보 경로에 포함

- 같은 API는 중복 요청하지 않기
