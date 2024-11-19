---
layout: single
title: "[CodingTest]코딩 테스트 스터디"
categories: boj
toc: true
toc_sticky: true
---

# 우테코 코딩테스트 알고리즘 스터디

## 💻240715~240721

1\. 달리기 경주(Lv.1)
[178871](https://school.programmers.co.kr/learn/courses/30/lessons/178871)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <b>해시</b>   
  <p>
    <div>• 파이썬 내장함수 list.index()를 사용하면 O(n)이기 때문에 시간 초과가 난다.</div>
    <div>• 플레이어 초기 등수를 dict{이름: 랭킹}에 저장한다.</div>
    <div>• 이름이 불린 선수의 랭킹-1, 그 앞 선수의 랭킹+1을 한다.</div>
    <div>• 무결성을 유지하기 위해 리스트의 순서도 바꿔준다.</div>

  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/9e388306c2d9608f91d4a92adff74c4a.js"></script>

<br>
<br>

## 💻240722~240728

1\. 도서관(골5)
[1461](https://www.acmicpc.net/problem/1461)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <b>그리디</b>
  <p>
    <div>음수</div>
    <div>• 음수는 오름차순으로 정렬하면 0번째가 제일 멀리있다.</div>
    <div>• M으로 나눈 나머지가 0인 곳만 더해준다.</div>
    <div>• 나머지가 0인 곳만 궁금하고 나머지는 궁금하지 않기 때문</div>
    <div>ex)
      <mark class="yellow">-45</mark> -26 -18
      <mark class="yellow">-9</mark> -4 22 40 50
    </div>

  </p>
  <p>
    <div>양수</div>
    <div>• 양수는 내림차순으로 정렬하면 0번째가 제일 멀리있다.</div>
    <div>• M으로 나눈 나머지가 0인 곳만 더해준다.</div>
    <div>ex)
      <mark class="yellow">50</mark> 40 22 -4 -9 -18 -26 -45
    </div>
  </p>
  <p>
    <div>최종 계산</div>
    <div>• 제일 멀리 있는 곳에 도착해서 끝나는게 정답이다.</div>
    <div>• 제일 멀리 있는 곳 빼고 다 x2한 값을 더한다.</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/1f161e4e0f07a35c43ced1ec45be23a8.js"></script>

<br>

2\. 결혼식(실2)
[5567](https://www.acmicpc.net/problem/5567)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <b>BFS</b>   
  <p>
    <div>• 건너건너 친구까지이기 때문에 depth 2까지만 센다.</div>
    <div>• visited에 depth를 저장한다.</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/70543e08364ed1447c5b52fddd42f480.js"></script>

<br>

3\. 점프 ( 실1)
[1890](https://www.acmicpc.net/problem/1890)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <b>DP</b>   
  <p>
    <div>• 현재 적혀있는 수만큼 오른쪽, 아래쪽 answer에 현재 위치의 answer를 더한다.</div>
    <div>• 그렇게 값을 누적해나가면 도착지로 갈 수 있는 경우의 수가 다 더해진다.</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/85099da31cfa01d10e7b6db9307990a2.js"></script>

<br>

4\. 죽음의 비 (골4)
[22944](https://www.acmicpc.net/problem/22944)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <b>BFS, 구현</b>   
  <p>
    <div>• visited는 3차원으로 할 필요없이 '체력+우산내구도'를 저장하면 된다.</div>
    <div>• 주의: 우산을 새로 쓴 곳도 비가 오기 때문에 30번째 줄에 nd = D가 아닌 nd = D-1이다.</div>
    <div>• 끝까지 E를 만나지 못 한다면 while문을 탈출하여 -1를 출력한다.</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/01aa7980633eba48ffa3e06908f16b26.js"></script>

<br>
<br>

## 💻240729~240804

1\. 안정적인 문자열 (실1)
[4889](https://www.acmicpc.net/problem/4889)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <b>그리디, 스택</b>   
  <p>
    <div>• '}'인데 stack이 비어있으면 일단 cnt+1을 해주고 stack에 뒤집어서 '{'로 넣어준다.</div>
    <div>• '{'가 추후에 '}'를 만나 사라질 수 있게 하기 위해서다.</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/249feac8fb8bcc9ee3d2f0994b70a538.js"></script>

<br>

3\. 농장 관리 (골5)
[1245](https://www.acmicpc.net/problem/1245)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <b>BFS</b>   
  <p>
    <div>• 입력 받으면서 max_height를 구한 후 0부터 max_height까지 돌면서 해당 높이를 만날 때 마다 bfs를 호출한다.</div>
    <div>• bfs의 용도는 '봉우리 존재 여부 반환'과 '방문 처리'이다.</div>
    <div>• 기본적인 bfs의 코드와 동일하며 '봉우리인지'를 찾기 위해 16, 27, 28번째 줄이 추가되었다.</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/45724d4ecaa71408559de0da67fb5c7b.js"></script>

<br>

4\. 꿈틀꿈틀 호석 애벌레 - 기능성 (실1)
[20167](https://www.acmicpc.net/problem/20167)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <b>재귀</b>   
  <p>
    <div>dp로 나와있지만 기능성은 재귀로도 시간초과 없이 풀 수 있다. 효율성은 dp로 다시 생각해 봐야 할 듯..?</div>
  </p>
  <p>
    <div>1. 이전 total값이 K보다 큰 경우</div>
    <div>최소 만족도가 K 이상이면 먹는 것을 멈춘다고 나와있으므로 에너지를 계산한 후 0으로 초기화 한다.</div>
  </p>
  <p>
    <div>2. 이전 total값이 K보다 작은 경우</div>
    <div>• 이전 total이 0이면, 현재는 안 먹을 수도 안 먹을 수도 있다.</div>
    <div>• 이전 total이 0이 아니면, 무조건 연속적으로 먹어야 하므로 현재 만족도를 더해준다.</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/8864838dd9ca409816dacaa086fdbeae.js"></script>
