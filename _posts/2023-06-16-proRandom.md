---
layout: archive
title: "프로그래머스 랜덤"
categories: CodingTest
toc: true
toc_sticky: true
---

# Programmers Random

## 💻20230617~20230623 (LV. 2)

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
