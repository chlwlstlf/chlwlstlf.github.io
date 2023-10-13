---
layout: single
title: "[Programmers]프로그래머스 랜덤"
categories: programmers
toc: true
toc_sticky: true
---

# Programmers Random

## 💻230617~230623 (LV. 2)

1\. 숫자의 표현 (Lv. 2) [12924](https://school.programmers.co.kr/learn/courses/30/lessons/12924)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    0부터 n까지 숫자 하나씩 올려서 돌리면서 n이면 answer += 1, n보다 커지면 break
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/65d70ac9f6ca69771d7e206e9eb2e468.js"></script>

---

2\. 스킬트리 (Lv. 2) [49993](https://school.programmers.co.kr/learn/courses/30/lessons/49993)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>skill이 "CBD"이면 C를 무조건 해야 B를 할 수 있음.</div>
    <div>문자열에서 C를 발견하면 i+=1을 하여 문자열 B가 나올 때까지 문자열 돌림</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/535662ee6dfc7e0b6da385fc9c6fb367.js"></script>

---

3\. 예상 대진표 (Lv. 2) [12985](https://school.programmers.co.kr/learn/courses/30/lessons/12985)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    (4, 7) → ((4+1)//2=2, (7+1)//2=4) → ((2+1)//2=1, (4+1)//2=2) → ((1+1)//2=1, (2+1)//2=1), cnt는 3
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/c9244301fa440ec3bf81c6c678d0cbc1.js"></script>

---

4\. [1차] 뉴스 클러스터링 (Lv. 2) [17677](https://school.programmers.co.kr/learn/courses/30/lessons/17677)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>1. str.lower()로 모두 소문자로 바꾼 후 비교</div>
    <div>2. 교집합은 set1 원소가 set2에 있으면 교집합 배열에 넣은 후 set2에서 해당 원소 삭제</div>
    <div>3. 합집합은 {set1 + set2 - 교집합}</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/13252a57f4a28939f6330436b3bc5269.js"></script>

---

5\. [1차] 캐시 (Lv. 2) [17680](https://school.programmers.co.kr/learn/courses/30/lessons/17680)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>최근 사용한 것을 제일 처음에 넣어주고</div>
    <div>개수가 넘어가면 제일 뒤에 있는 원소 pop하기</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/370418e9cc729518d400238da1c6a185.js"></script>

## 💻230916~230922 (LV. 2)

1\. 주차 요금 계산 (Lv. 2) [92341](https://school.programmers.co.kr/learn/courses/30/lessons/92341)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>dict1: 입차 딕셔너리</div>
    <div>dict2: 시간 계산 결과 딕셔너리</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/318f718d835338df581e64926fcbb042.js"></script>

---

2\. k진수에서 소수 개수 구하기 (Lv. 2) [92335](https://school.programmers.co.kr/learn/courses/30/lessons/92335)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>범위를 (2, int(math.sqrt(n))+1) 이렇게 안 해주면 시간초과남</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/39fc4e09b8f77071688779fe3394f99c.js"></script>

---

3\. 두 큐 합 같게 만들기 (Lv. 2) [118667](https://school.programmers.co.kr/learn/courses/30/lessons/118667)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>9번째 줄 *3인 이유</div>
    <div>length가 최대일 때는</div>
    <div>1. a에서 b로 다 넘김 -> 길이 n</div>
    <div>2. b에 있는 (a+b)를 a로 다 넘김 -> 길이 2n</div>
    <div>따라서 최대 길이는 3n임</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/af9bf7425c77f70328f76f61e9691c78.js"></script>

## 💻230923~231006 (LV. 2)

1\. 튜플 (Lv. 2) [64065](https://school.programmers.co.kr/learn/courses/30/lessons/64065)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>eval: 문자열로 식을 입력하면 해당식을 실행한 결과값을 반환</div>
    <div>{} 기호는 인식 못 함(TypeError: unhashable type: 'set')</div>
    <div>[]로 replace 해주고 eval 하면 해결</div>
  </p>
  <p>
    <div>길이 짧은 순으로 정렬</div>
    <div>answer에 해당 숫자가 없다면 추가하기</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/a8e3b928894b5821fa92af9db1cf4884.js"></script>

---

2\. [3차] 압축 (Lv. 2) [17684](https://school.programmers.co.kr/learn/courses/30/lessons/17684)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>1. A~Z 까지의 딕셔너리 만들기</div>
    <div>2. 현재 입력 W는 이전 반복문의 다음 글자(c)임</div>
    <div>3. W('KA')가 dict에 없다면</div>
    <div>4. dic['KA'] = 27로 해주고 j의 값 증가시킴</div>
    <div>5. c(다음 글자)는 'A', w(현재 입력)는 'K'</div>
    <div>6. w의 딕셔너리 값(dic['K']=11)을 answer에 넣음</div>
    <div>7. i를 w길이 만큼 증가시켜서 다음 반복문은 'A'부터 시작하게 함</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/6d6f572a901114639110a4d558c28188.js"></script>

---

3\. [3차] n진수 게임 (Lv. 2) [17687](https://school.programmers.co.kr/learn/courses/30/lessons/17687)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>notation은 진법 바꾸는 함수</div>
    <div>answer2는 전체 학생들이 말하는 수</div>
    <div>answer는 answer2에서 튜브가 말해야하는 수</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/58b0925b1054117a229a9804c63fa042.js"></script>

---

4\. 오픈채팅방 (Lv. 2) [42888](https://school.programmers.co.kr/learn/courses/30/lessons/42888)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>Enter, Change일 떄 세번째 값이 닉네임이므로 같은 아이디에 닉네임을 갱신해줌(안 바껴도 갱신해줌)</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/5f8e28dc8b37790bf72bafeefd2fb869.js"></script>

---

5\. [3차] 파일명 정렬 (Lv. 2) [17686](https://school.programmers.co.kr/learn/courses/30/lessons/17686)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>무조건 영문자로 시작하고 숫자가 있으므로</div>
    <div>처음부터 돌다가 숫자가 나오면 그 이전 글자까지 소문자로 바꾼 후 배열 두 번째 값에 넣고,</div>
    <div>숫자를 돌다가 영문자가 나오거나 파일명이 끝나면 숫자로 바꾼 후 배열 세 번째 값에 넣음</div>
  </p>
  <p>
    <div>※ 소문자와 숫자로 바뀌기 때문에 원래의 파일명을 알지 못 함 -> 원래 파일명의 인덱스 값을 배열 첫 번째 값에 미리 넣어주기</div>
    <div>※ TAIL이 없는 경우도 생각해야함</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/e959e85a4ae17e225028ae5ab40f1b77.js"></script>

---

6\. [1차] 프렌즈4블록 (Lv. 2) [17679](https://school.programmers.co.kr/learn/courses/30/lessons/17679)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>1. 세로로 내려오게 하는 건 매우 힘듦 -> 전치 행렬로 바꾸자</div>
    <div>2. 2x2를 찾은 후 바로 삭제하면 여러 2×2에 포함되는 부분은 지워지지 않을 수도 있음 -> 지워야하는 부분의 인덱스를 모두 집합에 추가한 후 나중에 지우기</div>
    <div>3. 해당 인덱스를 0으로 바꾼 후 0인 부분을 지우고 지워진 개수만큼 배열 앞에 0을 추가 -> 글자를 뒤로 밀어서 떨어지는 것을 구현함</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/ccd336366b4f02dfcd569860ed5b822f.js"></script>

## 💻231007~231013 (LV. 1)

https://school.programmers.co.kr/learn/courses/30/lessons/81301
https://school.programmers.co.kr/learn/courses/30/lessons/17681
https://school.programmers.co.kr/learn/courses/30/lessons/42889
https://school.programmers.co.kr/learn/courses/30/lessons/17682
https://school.programmers.co.kr/learn/courses/30/lessons/77484
