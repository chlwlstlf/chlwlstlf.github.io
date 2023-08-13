---
layout: archive
title: "코딩테스트 준비"
categories: CodingTest
toc: true
toc_sticky: true
---

# Coding Test Summary

## 이진 탐색

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>1. start, end 범위 정하기</p>
  <p>2. while start <= end: 후 mid = (start+end)//2</p>
  <p>3. 보통 for문을 포함한 코드를 짬</p>
  <p>4. start = mid + 1 또는 end = mid - 1</p>
  <p>5. result는 문제 조건에 따라 필요한 곳에 넣음</p>
</div>

코드(python)

```python
start = #(1)보통 0이나 최솟값
end = #(1)보통 최댓값이나 매우 큰 수

while start <= end: #(2)
  mid = (start+end)//2 #(2)

  #(3)코드 짜기

  if 특정 조건 :
    start = mid + 1 #(4)
    result = mid #(5)조건에 따라 end밑에 쓸 수도 있음
  else:
    end = mid - 1 #(4)
```

예시 코드(python)  
[BOJ 2805]나무 자르기 (실2)  
:grey_question:한 줄에 연속해 있는 나무들을 땅으로부터 H미터 올라간 부분에서 자른다. 나무를 필요한 만큼만 집으로 가져가려고 할 때 적어도 나무를 M미터 가져가기 위해 절단기에 설정할 수 있는 높이의 최댓값은 무엇인가?

입력 예시  
4 7 #나무 4개, 7미터 가져가려고 함  
20 15 10 17

출력 예시  
15 #5+0+0+2 = 7

```python
import sys
input = sys.stdin.readline

N, M = map(int, input().split())
T = list(map(int, input().split()))

start = 0
end = max(T)
while start <= end :
  mid = (start+end)//2
  total = 0
  for i in range(N):
    if T[i] > mid:
      total += T[i]-mid
  if total < M :
    end = mid-1
  else : #M보다 total이 크거나 같으므로 result값 갱신
    start = mid+1
    result = mid

print(result)
```

## dfs

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>1. graph 생성하기(단방향, 양방향 구분)</p>
  <p>2. visited 배열 0으로 초기화(배열은 전역 변수임)</p>
  <p>3. dfs(v): 후 visited[v] = 1로 갱신</p>
  <p>4. v와 연결 되어있는 곳 중에서 방문하지 않은 곳에서 dfs 호출</p>
</div>

코드(python)

```python
graph = [[], [], [], []] #(1)
visited = [0]*(n+1) #(2)

def dfs(v):
  visited[v] = 1 #(3)
  for i in graph[v]: #(4)
    if visited[i] == 0:
      dfs(i)
```

예시 코드(python)  
[BOJ 2606]바이러스(실3)  
:grey_question:컴퓨터가 서로 연결되어 있으면 바이러스에 걸리게 된다. 1번 컴퓨터가 바이러스에 걸렸을 때 바이러스가 걸리게 되는 컴퓨터 수는 몇 대인가?

입력 예시  
7 #컴퓨터 수  
6 #간선 개수  
1 2  
2 3  
1 5  
5 2  
5 6  
4 7

출력 예시  
4

```python
import sys
input = sys.stdin.readline

#입력
N = int(input())
E = int(input())
arr = [list(map(int, input().split())) for _ in range(E)]

#그래프 생성
graph = [[] for i in range(N+1)]
for i in range(E):
  graph[arr[i][0]].append(arr[i][1])
  graph[arr[i][1]].append(arr[i][0]) #양방향임

#dfs
def dfs(v, visited):
  visited[v] = 1
  for i in graph[v]:
    if not visited[i]:
      dfs(i, visited)

#dfs함수 부르고 출력
visited = [0]*(N+1)
dfs(1, visited)
print(visited.count(1)-1)
```

## bfs

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>1. from collections import deque</p>
  <p>2. q = deque() 후 q.append(bfs 인자)</p>
  <p>3. while q: q.popleft()</p>
  <p>4. 보통 for문을 포함한 코드를 짬</p>
  <p>5. graph[nx][ny] = graph[x][y] + 1 후 q.append((nx, ny))</p>
  <p>6. 마지막 지점에 도달하면 return 마지막 지점</p>
</div>

코드(python)

```python
from collections import deque #(1)

def bfs(x):
  q = deque() #(2)
  q.append(x) #(2)

  while q: #(3)
    nx = q.popleft() #(3)

    for 범위: #(4)for문을 이용한 코드
      if 특정 조건:
        visited[i] = visited[nx] + 1 #(5)전 단계에서 하나를 더함
        q.append(nx) #(5)q에 새로 넣어줌
    if visited[target] != 0: #(6)마지막 지점에 도달하면 그 지점의 값이 0에서 다른 걸로 바껴있음
      return visited[target] #(6)그냥 마지막 지점을 return하면 됨

bfs(k)
```

예시 코드(python)  
[Programmers 1844]게임 맵 최단거리 (Level 2)  
:grey_question:(0, 0)에서 (n, m)으로 갈 수 있는 최단 거리를 구하려고 한다. 0은 막혀있는 부분이고 1인 곳으로만 갈 수 있다. 막혀서 도착할 수 없는 경우에는 -1을 return한다. 최단거리는 무엇인가?

입력 예시  
[[1,0,1,1,1],[1,0,1,0,1],[1,0,1,1,1],[1,1,1,0,1],[0,0,0,0,1]]

출력 예시  
11

```python
from collections import deque

def solution(maps):
    def bfs (x, y):
        q = deque()
        q.append((x, y))
        while q:
            x, y = q.popleft()
            for i in range(4):
                nx = x+dx[i]
                ny = y+dy[i]
                if nx < 0 or nx >= len(maps) or ny < 0 or ny >= len(maps[0]):
                    continue
                if maps[nx][ny] == 0:
                    continue
                if maps[nx][ny] == 1:
                    maps[nx][ny] = maps[x][y]+1
                    q.append((nx, ny))
        if maps[len(maps)-1][len(maps[0])-1] <= 1:
            return -1
        else :
            return maps[len(maps)-1][len(maps[0])-1]


    dx = [0, 1, -1, 0]
    dy = [1, 0, 0, -1]


    return bfs(0, 0)
```

입력 maps  
1 0 1 1 1  
1 0 1 0 1  
1 0 1 1 1  
1 1 1 0 1  
0 0 0 0 1

최종 maps  
3 0 9 10 11  
2 0 8 0 10  
3 0 7 8 9  
4 5 6 0 10  
0 0 0 0 11
