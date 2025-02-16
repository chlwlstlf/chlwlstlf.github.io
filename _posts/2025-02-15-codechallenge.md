---
layout: single
title: "[Programmers] 2025 프로그래머스 코드챌린지"
categories: programmers
toc: true
toc_sticky: true
---

# Programmers CodeChallenge

[코드챌린지 링크](https://career.programmers.co.kr/competitions/4079){: .btn .btn--primary}

## 💻20250208 (Lv.1~3)

**1\. 유연근무제 (Lv.1)**
[388351](https://school.programmers.co.kr/learn/courses/30/lessons/388351)

<div class="blue-box">
  <p>
    <b>구현</b>
    <div>• 시간이 100 단위기 때문에 60 단위로 다시 바꿔주어야 한다.</div>
    <div>• startday로 현재 요일을 알아낸 후에 주말이면 건너뛴다.</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/d9299414a8ace9b6b361f89314f99702.js"></script>

<br>

**2\. 비밀 코드 해독 (Lv.2)**
[388352](https://school.programmers.co.kr/learn/courses/30/lessons/388352)

<div class="blue-box">
  <p>
    <b>조합</b>
    <div>• 5개 숫자 조합을 만든다.</div>
    <div>• 그 조합과 q[i]의 합집합 개수가 10-ans[i]와 한 번이라도 다르면 정답 후보가 될 수 없으므로 False를 return한다.</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/e9ed2aacad184295b75c11abc54c265b.js"></script>

<br>

**3\. 지게차와 크레인 (Lv.2)**
[388353](https://school.programmers.co.kr/learn/courses/30/lessons/388353)

<div class="blue-box">
  <p>
    <b>BFS</b>
    <div>• air는 매번 arr의 결과를 deepcopy한 배열이다.</div>
    <div>• "."는 외부 빈칸, "*"는 내부 빈칸이다.</div>
    <div>• "."에서 BFS를 돌렸을 때 "*"를 만나면 이 칸도 이제는 외부인 것으므로 "."으로 변경해준다.</div>
    <br>
    <div>• air에서 주변에 "."이 있다면 해당 알파벳도 외부이므로 "."으로 변경한다.</div>
    <div>• air에서 주변에 "."이 없었다면(f==0) 크레인인지 확인하고 크레인이면 내부 칸도 꺼낼 수 있으므로 "*"으로 변경한다.</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/618ee524297a2a4cb80cfa643bd56506.js"></script>

<br>

**4\. 홀짝트리 (Lv.3)**
[388354](https://school.programmers.co.kr/learn/courses/30/lessons/388354)

<div class="blue-box">
  <p>
    <b>DFS</b>
    <div>• dfs 함수에서 result는 특정 노드가 속해있는 트리의 노드들이다.</div>
    <div>• root는 특정 노드가 root일 때 0이면 홀짝노드, 1이면 역홀짝노드임을 나타낸다.</div>
    <div>※ 특정 노드가 root일 때 홀짝노드이면 root가 아닐 때는 무조건 역홀짝노드이다.</div>
    <br>
    <div>• root가 0인 것의 개수와 1인 것의 개수를 모두 구한 후 0이 1개라면 홀짝트리, 1이 1개라면 역홀짝트리로 정의한다.</div>
    <div>• 그 이유는 root 홀짝노드가 1개이면 그 노드가 루트일 때 나머지 노드들은 루트일 때 역홀짝노드이므로 루트가 아니면 홀짝노드가 되기 때문이다.</div>
  </p>
</div>

![image](https://grepp-programmers.s3.ap-northeast-2.amazonaws.com/files/production/f97d7758-a479-40ae-8f88-d989a3d331be/%E1%84%86%E1%85%AE%E1%84%8C%E1%85%A6.drawio%20%287%29.png)

이 사진 예시일 때 `노드`와 `root[노드]`를 출력하면 아래와 같이 나온다.

```
3 0
4 1
6 1
2 1
```

3: 루트일 때 홀짝노드  
4, 6, 2: 루트일 때 역홀짝노드  
이때 3이 루트가 되면 4, 6, 2도 모두 홀짝 노드가 되므로 이는 홀짝트리이다.

<script src="https://gist.github.com/chlwlstlf/a5f2cd90a0b8930348351815a719e8bc.js"></script>

<br>
<br>

## 💻20250211 (Lv.1~3)

**1\. 택배 상자 꺼내기 (Lv.1)**
[389478](https://school.programmers.co.kr/learn/courses/30/lessons/389478)

<div class="blue-box">
  <p>
    <b>조합</b>
    <img src="https://grepp-programmers.s3.ap-northeast-2.amazonaws.com/files/production/e06b4c0d-0ce6-4a2d-8ad4-ba20f9398145/ex1-1.png" alt="">
    <div>사진은 밑에서 위로 가지만 계산 편의상 위에서부터 시작한다. 또 배열 개수를 똑같게 하기 위해 22개까지만 있어도 24개까지 배열을 만든다.</div>
    <br>
    <div>1 2 3 4 5 6</div>
    <div>12 11 10 9 8 7</div>
    <div>13 14 15 16 17 18</div>
    <div>24 23 22 21 20 19</div>
    <br>
    <div>행을 키우면서 n보다 작은 값이면 정답에 추가한다.</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/6bdcf0e6135c0e87e4da8164c9e834eb.js"></script>

<br>

**2\. 서버 증설 횟수 (Lv.2)**
[389479](https://school.programmers.co.kr/learn/courses/30/lessons/389479)

<div class="blue-box">
  <p>
    <b>시뮬레이션</b>
    <div>• answer에는 서버의 운영 시간을 넣고, 한 시간이 지날 때마다 모든 서버의 운영 시간을 1씩 줄인다.</div>
    <div>• 한 턴이 지나면 answer를 먼저 줄인 후 남은 서버의 수를 센다.</div>
    <div>• 남은 서버 수 보다 필요한 서버 수가 크면 서버를 추가한다.</div>
  </p>
</div>

<script src="https://gist.github.com/chlwlstlf/62446c6a54fec18ad586cc39d3ebdd61.js"></script>

<br>

**3\. 완전범죄 (Lv.2)**
[389480](https://school.programmers.co.kr/learn/courses/30/lessons/389480)

<div class="blue-box">
  <p>
    <b>DP+BFS</b>
    <div>• nxm dp 배열을 만든다. dp[2][3]=2이면 2번째 물건까지 훔쳤을 때 A의 흔적은 2이고, B의 흔적은 3이라는 뜻이다.</div>
    <div>• x와 y에 info[i]를 더하면서 BFS를 돈다. 이때 nx는 A가 훔치는 것이고, ny는 B가 훔치는 것이다.</div>
    <div>• dp에는 i+1를 저장하고 A가 최소여야 하므로 마지막까지 다 훔쳤을 때 가장 위에 행에 위치한 것이 정답이다.</div>
  </p>
</div>

[예제 1] dp 결과 출력

```
0 0 1 0
1 0 0 2
0 0 2 3 -> 가장 위에 행에 위치한 3이다.
2 3 0 3
```

<script src="https://gist.github.com/chlwlstlf/34bde80285c9820fee566093039941c1.js"></script>

<br>

**4\. 봉인된 주문 (Lv.3)**
[389481](https://school.programmers.co.kr/learn/courses/30/lessons/389481)

<div class="blue-box">
  <p>
    <b>수학</b>
    <div>• 알파벳을 활용한 26진법이라고 생각하면된다. 다만 나머지 연산을 했을 때 0이 "a"이고 25가 "z"이므로 나머지 연산 전에 n을 1 빼준 후 계산한다.</div>
    <div>• answer 초깃값은 n과 bans의 길이 합이다.</div>
    <div>• bans를 돌면서 answer보다 큰 것의 개수를 세서 prev와 같은지 확인한다.</div>
    <div>• 그 이유는 answer를 변경했을 때 답이 달라질 수 있기 때문이다.</div>
    <div>• 현재 제외한 개수와 prev 값이 같을 때까지 answer를 변경해가며 계산을 한다.</div>
    <br>
    <div>예를 들어 n은 2이고, bans는 ["a", "b", "e", "f"]일 때 answer는 f -> e -> d 순서로 변하게 된다.</div>
  </p>
</div>

n = 2, bans = ["a", "b", "e", "f"] 일 때,  
answer, prev, cnt 출력 결과

```
answer: f prev: -1 cnt: 1
answer: e prev: 1 cnt: 2
answer: d prev: 2 cnt: 2
```

<script src="https://gist.github.com/chlwlstlf/af5d197af3d85da88c0d138df0babcd6.js"></script>
