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

## 💻230923~231006 (LV. 2~LV. 3)

1\. [64065](https://school.programmers.co.kr/learn/courses/30/lessons/64065)

2\. [17684](https://school.programmers.co.kr/learn/courses/30/lessons/17684)

3\. [17687](https://school.programmers.co.kr/learn/courses/30/lessons/17687)

4\. [42888](https://school.programmers.co.kr/learn/courses/30/lessons/42888)

5\. [17686](https://school.programmers.co.kr/learn/courses/30/lessons/17686)

6\. [17679](https://school.programmers.co.kr/learn/courses/30/lessons/17679)
