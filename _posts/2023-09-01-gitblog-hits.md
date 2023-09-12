---
layout: single
title: "[gitblog] 방문자 수 추가하기"
categories: etc
toc: true
toc_sticky: true
---

# Hits 사용하기

## 뱃지 만들기

[hits 사이트](https://hits.seeyoufarm.com)

![1](https://github.com/chlwlstlf/data/assets/63334368/17a7e30e-4f63-4413-9131-bb82f0a974bd)

1. \+ Add Icon: 뱃지 제일 앞에 아이콘 추가
2. ICON COLOR: 아이콘 색깔 변경
3. BORDER: ROUND | FLAT 뱃지 모서리
4. TITLE: 제목
5. TITLE BG COLOR: 제목 배경색
6. COUNT BG COLOR: 방문수 배경색

## 코드 복사하기

![2](https://github.com/chlwlstlf/data/assets/63334368/65786571-55b5-4ee0-a78b-9e1657ee9382)

- 깃블로그 html 파일에 추가할 것이기 때문에 `HTML LINK` 코드를 복사했다.

## sidebar.html에 코드 추가하기

![3](https://github.com/chlwlstlf/data/assets/63334368/edbf7288-a66e-4c14-bef7-1cd5972ce067)

- 사이드바 제목 바로 아래에 나오길 원해서 그 아래에 코드 추가했다.
- 뱃지 크기 이슈 때문에 img에 hits 클래스를 추가해준 후 style로 크기 조정했다.

## 결과 확인하기

![4](https://github.com/chlwlstlf/data/assets/63334368/ef1f8776-873b-4628-a34c-4be483f566e0)

- 내가 원하는 곳에 잘 들어간 것을 볼 수 있다.
- 블로그와 색을 통일하게 위해 분홍색을 골랐지만 각자 원하는 색상으로 뱃지를 만들면 된다.
- `https://chlwlstlf.github.io`로 시작하는 모든 url를 방문할 때마다 방문수가 올라가서 정확하진 않다.(포스트 하나 들어가도 방문자 수가 올라간다.)
