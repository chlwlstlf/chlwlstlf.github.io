---
layout: single
title: "[우테코]Level1 페어프로그래밍 제출 방법"
categories: woowacourse
toc: true
toc_sticky: true
---

# 1단계, 2단계 제출 방법

## <mark class="pink">📝pair와 구현한 코드 내보내기</mark>

(내 vscode에서 구현했다고 가정)

1\. pair와 연결하기

- pair는 본인이 포크한 저장소에 `step1` 브랜치를 생성
- pair가 저장소에서 나를 colaborator로 추가

<br>

2\. pair 저장소 추가 & push

✔️ second는 저장소 별칭

```
git remote add second {pair의 저장소 url}
git push second step1 //pair의 step1 브랜치에 push
```

<br>
<br>

## <mark class="pink">📝Pull Request에 들어갈 내용</mark>

1\. 어떻게 해결하려고 했는 지  
2\. (덜 됐다면) 덜 된 이유  
3\. 글 다듬어서 쓰기

<br>
<br>

## <mark class="pink">📝미션 2단계 제출 방법</mark>

1\. merge를 완료했다는 통보를 받으면 브랜치 변경 및 작업 브랜치 삭제(option)한다.

```
git checkout main
git branch -D step1
```

<br>

2\. 통합(merge)한 woowacourse 저장소와 동기화하기 위해 woowacourse 저장소 추가

✔️ upstream은 저장소 별칭

```
git remote add upstream https://github.com/woowacourse/javascript-racingcar.git
```

<br>

3\. woowacourse 저장소에서 자기 브랜치 가져오기(또는 갱신하기)

```
git fetch upstream chlwlstlf
git branch -a //remotes/upstream/chlwlstlf 브랜치 생성 확인
```

<br>

4\. woowacourse 저장소 브랜치와 동기화하기

✔️ rebase는 다른 브랜치에서 진행된 커밋을 다시 가져와서 base를 재설정하는 것

```
git rebase upstream/chlwlstlf
```

<div class="blue-box">
  <p>
    <p>⚠️ rebase에서 충돌이 떴다. 커밋 충돌인 것 같다.</p>
    <div><<<<<< HEAD 위에 있는 글씨들이 버튼이다.</div>
    <div>나는 현재 변경 사항이 제일 최근 상태이기 때문에 첫 번째 버튼을 눌러 바꿔 주었다.</div>
    <img src="https://github.com/chlwlstlf/data/assets/63334368/fe91d4a4-de41-4baf-952d-bad4c4ca40c1" alt="충돌 사진"/>
  </p>
  <p>
    <div>- Accept Current Change: 현재 변경 사항</div>
    <div>- Accept Incoming Change</div>
    <div>- Accept Both Changes</div>
    <div>- Compare Changes</div>
  </p>
</div>

<br>

5\. main 브랜치에 푸시하기

<br>

6\. stetp2 브랜치 만든 후 main 브랜치 풀 받기
