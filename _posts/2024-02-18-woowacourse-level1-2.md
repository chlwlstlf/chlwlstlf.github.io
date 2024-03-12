---
layout: single
title: "Level1 자동차 경주 게임 2단계"
categories: 우아한테크코스
toc: true
toc_sticky: true
---

# 🚗 자동차 경주 게임 리팩토링

## <mark style='background-color: #ffdce0'>🖇Pull Request에 들어갈 내용</mark>

1\. 어떻게 해결하려고 했는 지  
2\. (덜 됐다면) 덜 된 이유  
3\. 글 다듬어서 쓰기

---

## <mark style='background-color: #ffdce0'>📁미션 2단계 제출 방법</mark>

1\. merge를 완료했다는 통보를 받으면 브랜치 변경 및 작업 브랜치 삭제(option)한다.

```
git checkout chlwlstlf
git branch -D step1
```

2\. 통합(merge)한 woowacourse 저장소와 동기화하기 위해 woowacourse 저장소 추가

✔️ upstream은 저장소 별칭

```
git remote add upstream https://github.com/woowacourse/javascript-racingcar.git
```

3\. woowacourse 저장소에서 자기 브랜치 가져오기(또는 갱신하기)

```
git fetch upstream chlwlstlf
git branch -a //remotes/upstream/chlwlstlf 브랜치 생성 확인
```

4\. woowacourse 저장소 브랜치와 동기화하기

✔️ rebase는 다른 브랜치에서 진행된 커밋을 다시 가져와서 base를 재설정하는 것

```
git rebase upstream/chlwlstlf
```

⚠️ rebase에서 충돌이 떴다. 커밋 충돌인 것 같다.

<<<<<< HEAD 위에 있는 글씨들이 버튼이다.

나는 현재 변경 사항이 제일 최근 상태이기 때문에 첫 번째 버튼을 눌러 바꿔 주었다.

![Untitled (4)](https://github.com/chlwlstlf/data/assets/63334368/fe91d4a4-de41-4baf-952d-bad4c4ca40c1)

- Accept Current Change: 현재 변경 사항
- Accept Incoming Change
- Accept Both Changes
- Compare Changes

5\. chlwlstlf 브랜치에 푸시하기

6\. stetp2 브랜치 만든 후 chlwltlf 브랜치 풀 받기

---

## <mark style='background-color: #ffdce0'>🚗자동차 경주 게임 리팩토링</mark>

**1단계 피드백 수정사항**

1\. 상수화 변경하기

- ‘:’ 과 ‘,’ 를 상수화 했었는데 상수 이름이 COLON과 COMMA였기 때문에 만약 ‘:’ 이 ‘-’으로 바뀌게 되면 COLON 이라는 이름도 바꿔야 한다고 생각해서 상수화한 것을 없앴습니다.
- 피드백을 반영하여 1을 FORWARD로 상수화하였습니다.

2\. 불필요한 class를 변경하기

- InputView, OutView, console은 안에 static 밖에 없어서 class 역할을 제대로 하지 못 하였습니다. 따라서 객체로 묶어서 쓰는 방식으로 변경했습니다.
- createRandom도 class였는데 여러 메소드나 인자를 묶어서 사용하지 않고 랜덤한 값을 return하는 기능만 하면 되기 때문에 함수로 변경했습니다. 또 createRandom이 자동차 경주에 직접적인 영향을 주지 않는다고 생각하여 utils 폴더에 넣어주었습니다. (꼭 랜덤이 아니어도 forward 조건은 문제에 따라 언제든지 바뀔 수 있다고 생각했습니다.)

**추가적인 수정사항**

재입력 오류 해결하기

- 1단계 때 재입력을 성공하지 못 하였는데 그 부분 테스트 코드를 짜지 않아 알지 못 하였습니다.
- 각 도메인 안에서 유효성 검사를 하고 있기 때문에 그 부분까지 묶어서 catchReturn 콜백 함수로 넘겨주었습니다.
