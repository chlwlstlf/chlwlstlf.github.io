---
layout: single
title:  "[Programmers] 코딩테스트 고득점 Kit"
categories: CodingTest
toc: true
toc_sticky: true
---

# Coding Test Study Algorithm

## 💻230204~230208 (Level 1~2)
1\. 폰켓몬 (Level 1)
[1845](https://school.programmers.co.kr/learn/courses/30/lessons/1845)
```python
def solution(nums):
  if len(set(nums)) > len(nums)//2 :
    return len(nums)//2
  else:
    return len(set(nums))
```
2\. 완주하지 못한 선수 (Level 1)
[42576](https://school.programmers.co.kr/learn/courses/30/lessons/42576)  
>시간초과

3\. 같은 숫자는 싫어 (Level 1)
[12906](https://school.programmers.co.kr/learn/courses/30/lessons/12906)
```python
def solution(nums):
  if len(set(nums)) > len(nums)//2 :
    return len(nums)//2
  else:
    return len(set(nums))
```
4\. K번째 수 (Level 1)
[42748](https://school.programmers.co.kr/learn/courses/30/lessons/42748)
```python
def solution(array, commands):
    answer = []
    for i in range(len(commands)):
        arr = array[commands[i][0]-1:commands[i][1]]
        arr.sort()
        answer.append(arr[commands[i][2]-1])
    return answer
```
5\. 최소직사각형 (Level 1)
[86491](https://school.programmers.co.kr/learn/courses/30/lessons/86491)
```python
def solution(sizes):
    r_max = 0
    c_max = 0
    for i in range (len(sizes)):
        if sizes[i][0] < sizes[i][1]:
            sizes[i][0], sizes[i][1] = sizes[i][1], sizes[i][0]
        if sizes[i][0] > r_max:
            r_max = sizes[i][0]
        if sizes[i][1] > c_max:
            c_max = sizes[i][1]
    return r_max*c_max
```
6\. 모의고사 (Level 1)
[42840](https://school.programmers.co.kr/learn/courses/30/lessons/42840)
```python
def solution(answers):
    answer = []
    result = []
    s = [[1, 2, 3, 4, 5], [2, 1, 2, 3, 2, 4, 2, 5], [3, 3, 1, 1, 2, 2, 4, 4, 5, 5]]
    for i in range(3):
        cnt = 0
        for j in range(len(answers)):
            if answers[j] == s[i][j%len(s[i])]:
                cnt += 1
        result.append(cnt)
        
    for i in range(3):
        if max(result) == result[i]:
            answer.append(i+1)
    return answer
```
7\. 체육복 (Level 1)
[42862](https://school.programmers.co.kr/learn/courses/30/lessons/42862)
```python
def solution(n, lost, reserve):
    arr = [0]*(n+1)
    for i in range(len(lost)):
        arr[lost[i]] -= 1
    for i in range(len(reserve)):
        arr[reserve[i]] += 1
    
    for i in range(n+1):
        if arr[i] == 1 and arr[i-1] == -1:
            arr[i] = 0
            arr[i-1] = 0
        if i != n and arr[i] == 1 and arr[i+1] == -1:
            arr[i] = 0
            arr[i+1] = 0
    answer = n - arr.count(-1)
    return answer
```
8\. 전화번호 목록 (Level 2)
[42577](https://school.programmers.co.kr/learn/courses/30/lessons/42577)
```python
def solution(phone_book):
    answer = True
    phone_book.sort()
    for i in range(len(phone_book)-1):
        pb = phone_book[i+1][:len(phone_book[i])] #startwith 쓰면 이 부분 필요 없음
        if pb == phone_book[i]:
            answer = False
            break
    return answer
```
9\. 위장 (Level 2)
[42578](https://school.programmers.co.kr/learn/courses/30/lessons/42578)
>시간초과

10\. 기능 개발 (Level 2)
[42586](https://school.programmers.co.kr/learn/courses/30/lessons/42586)
```python
import math

def solution(progresses, speeds):
    answer = []
    time = math.ceil((100.0-progresses[0])/speeds[0])
    count = 0
    
    for i in range(len(progresses)):
        if math.ceil((100.0-progresses[i])/speeds[i]) > time: #현재 시간보다 큰 시간이 나올 때 count값을 배열에 넣어줌
            answer.append(count)
            count = 0
            time = math.ceil((100.0-progresses[i])/speeds[i])
        count += 1
    answer.append(count)
    return answer
```

## 💻230209~230215 (Level 2)
11\. 올바른 괄호 (Level 2)
[12909](https://school.programmers.co.kr/learn/courses/30/lessons/12909)
```python
def solution(s):
    answer = True
    stack = []

    for i in range(len(s)):
        if s[i] == '(': #'('이면 stack에 넣기
            stack.append('(')
        else: #맨 처음 ')'가 아니고 stack이 비어있지 않으면 stack에 있는 마지막 '('을 없애기
            if i == 0 or len(stack) == 0:
                answer = False
                break
            else:
                stack.pop()
                
    if len(stack) != 0: #stack이 짝이 다 맞아서 비어있을 때만 True임
        answer = False
    return answer
```
12\. 프린터 (Level 2)
[42587](https://school.programmers.co.kr/learn/courses/30/lessons/42587)
```python
def solution(priorities, location):
    result = [i for i in range(len(priorities))] #순서 배열
    priorities2 = []
    result2 = []
    while True:
        if priorities[0] != max(priorities): #더 큰 수가 있다면 제일 앞에 있는 수를 제일 뒤로 둠
            priorities.append(priorities.pop(0))
            result.append(result.pop(0))
        else: #제일 큰 수라면 원래 배열에서 빼서 새 배열에 넣기
            priorities2.append(priorities.pop(0)) #priorities2는 내림차순 정렬 되어야함
            result2.append(result.pop(0))

        if len(priorities) == 0:
            break
            
    answer = result2.index(location)+1
    return answer
```
13\. 다리를 지나는 트럭 (Level 2)
[42583](https://school.programmers.co.kr/learn/courses/30/lessons/42583)
```python
def solution(bridge_length, weight, truck_weights):
    answer = 0
    cnt = [] #다리 위를 얼마나 건넜는지 판별
    i = 0
    stack = []
    
    while True:
        if i == len(truck_weights): #마지막 트럭이 다리에 올라가면 종료
            break
            
        if i > 0 and cnt[0] == bridge_length: #다리 위를 다 건넜으면 stack, cnt에서 pop
            stack.pop(0)
            cnt.pop(0)
            
        if sum(stack)+truck_weights[i] <= weight: #무게로 stack에 넣어도 되는지 판별
            stack.append(truck_weights[i])
            cnt.append(0)
            i += 1
            
        for j in range(len(cnt)):
            cnt[j] += 1
        answer += 1
        
    answer += bridge_length
    return answer
```
14\. 주식가격 (Level 2)
[42584](https://school.programmers.co.kr/learn/courses/30/lessons/42584)
```python
def solution(prices):
    answer = []
    for i in range(len(prices)):
        cnt = 0
        for j in range(i+1, len(prices)):
            cnt += 1
            if prices[i] > prices[j] or j == len(prices)-1:
                answer.append(cnt)
                break
    answer.append(0)
    return answer
```
15\. 더 맵게 (Level 2)
[42626](https://school.programmers.co.kr/learn/courses/30/lessons/42626)
>정확성 실패

16\. 가장 큰 수 (Level 2)
[42746](https://school.programmers.co.kr/learn/courses/30/lessons/42746)
```python
def solution(numbers):
    answer = ''
    numbers = list(map(lambda x:str(x)*3, numbers))
    numbers.sort(reverse=True)
    for i in range(len(numbers)):
        answer += numbers[i][:len(numbers[i])//3]   
    if int(answer)==0:
        answer = '0'
    return answer
```

17\. H-Index (Level 2)
[42747](https://school.programmers.co.kr/learn/courses/30/lessons/42747)
```python
def solution(citations):
    citations.sort(reverse=True)
    for i in range(len(citations)):
        if citations[i] <= i: #개수가 인용수보다 커지는 경곗값
            return i
    return len(citations)
```
18\. 소수 찾기 (Level 2)
[42839](https://school.programmers.co.kr/learn/courses/30/lessons/42839)
```python
from itertools import permutations

def solution(numbers):
    answer = 0
    arr = []
    numbers = list(map(int, numbers))
    
    #조합가능한 숫자들 구하기
    for i in range(len(numbers)):
        per = list(permutations(numbers, i+1))
        for p in per:
            arr.append(int(''.join(map(str, p))))
    arr = list(set(arr))

    #소수찾기
    for i in range(len(arr)):
        f = 0
        for j in range(2, arr[i]):
            if arr[i]%j == 0:
                f = 1
                break
        if f == 0 and arr[i] > 1:
            answer +=1
                       
    return answer
```
19\. 카펫 (Level 2)
[42842](https://school.programmers.co.kr/learn/courses/30/lessons/42842)
```python
def solution(brown, yellow):
    i = 1
    while True:
        j = yellow/i
        if (i+2)*(j+2) == (brown+yellow):
            answer = [i+2, j+2]
            break
        i += 1
    answer.sort(reverse=True)
    return answer
```
20\. 피로도 (Level 2)
[87946](https://school.programmers.co.kr/learn/courses/30/lessons/87946)
```python
from itertools import permutations

def solution(k, dungeons):
    answer = -1
    per = [i for i in range(len(dungeons))]
    per = list(permutations(per, len(dungeons)))
    
    for p in per:
        cnt = 0
        rest = k
        for j in p:
            if dungeons[j][0] <= rest:
                rest -= dungeons[j][1]
                cnt += 1
            else:
                break
        if answer < cnt :
            answer = cnt
    return answer
```

21\. 전력망을 둘로 나누기 (Level 2)
[86971](https://school.programmers.co.kr/learn/courses/30/lessons/86971)
```python
def dfs(graph, v, visited):
    visited[v] = 1
    for i in graph[v]:
        if not visited[i]:
            dfs(graph, i, visited)
            
def solution(n, wires):
    answer = n
    
    for i in range(n-1):
        graph = [[] for _ in range(n+1)]
        for j in range(n-1):
            if i != j:
                graph[wires[j][0]].append(wires[j][1])
                graph[wires[j][1]].append(wires[j][0])
        visited = [0]*(n+1)
        dfs(graph, 1, visited)
        if abs(visited.count(1)-visited.count(0)+1) < answer:
            answer = abs(visited.count(1)-visited.count(0)+1)
            
    return answer
```
22\. 모음사전 (Level 2)
[84512](https://school.programmers.co.kr/learn/courses/30/lessons/84512)
```python
arr = ['A', 'E', 'I', 'O', 'U']
count = 0

def dfs(n, word, cnt):
    global count
    count += 1
    if word == n:
        return True
    if cnt < 4 :
        for i in range(5):
            if dfs(n+arr[i], word, cnt+1)==True:
                return True #차례차례 위로 올라가면서 True를 반환

def solution(word):
    for i in range(5):
        if dfs(''+arr[i], word, 0) == True:
            return count
```
23\. 조이스틱 (Level 2)
[42860](https://school.programmers.co.kr/learn/courses/30/lessons/42860)
>못품(조건 너무 많음)

24\. 큰 수 만들기 (Level 2)
[42883](https://school.programmers.co.kr/learn/courses/30/lessons/42883)
```python
def solution(number, k):
    n = list(map(int, str(number)))
    total = len(n)-k
    idx = 0
    answer = ''
    while True:
        end = len(n)-total+1
        
        #최댓값 찾기
        max = 0
        for i in range(idx,end):
            if n[i] == 9: #시간 줄이기
                max = n[i]
                id = i+1
                break
            if n[i] > max:
                max = n[i]
                id = i+1
        
        answer += str(max)
        idx = id
        total -= 1
        if total == 0 or len(n)-idx==total:
            break
            
    if total > 0:
        answer += ''.join(map(str, n[idx:len(n)]))
    
    return answer
```
25\. 구명보트 (Level 2)
[42885](https://school.programmers.co.kr/learn/courses/30/lessons/42885)
```python
from collections import deque

def solution(people, limit):
    answer = 0
    people.sort()
    people = deque(people)

    while True:
        if len(people) <= 1:
            break
        if people[0]+people[-1] <= limit:
            people.popleft()
            people.pop()
            answer += 1
        else:
            people.pop()
            answer += 1  
            
    if len(people) > 0:
        answer += 1
    return answer
```
26\. 타겟 넘버 (Level 2)
[43165](https://school.programmers.co.kr/learn/courses/30/lessons/43165)
```python
def solution(numbers, target):
    answer = 0
        
    def dfs(cnt, total):
        nonlocal answer
        if cnt == len(numbers):
            if total == target:
                answer += 1
            return
        
        dfs(cnt+1, total+numbers[cnt])
        dfs(cnt+1, total-numbers[cnt])
    
    dfs(0, 0)
    return answer
```
27\. 게임 맵 최단거리 (Level 2)
[1844](https://school.programmers.co.kr/learn/courses/30/lessons/1844)
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

## 💻230216~230222 (Level 3)
28\. 베스트앨범 (Level 3)
[42579](https://school.programmers.co.kr/learn/courses/30/lessons/42579)  
```python
def solution(genres, plays):
    answer = [] 
    result = []
    genres_set = list(set(genres))
    
    #장르별로 합친 딕셔너리 만들기
    for i in range(len(genres_set)):
        n = {}
        for j in range(len(genres)):
            if genres[j] == genres_set[i]:
                n[j] = plays[j]
        result.append(n)
    
    #재생 횟수로 버블 정렬
    for i in range(len(genres_set)):
        for j in range(i, len(genres_set)):
            if sum(result[j].values())>sum(result[i].values()):
                result[i], result[j] = result[j], result[i]

    #장르 내에서 많이 재생된 노래 순으로 정렬
    for i in range(len(genres_set)):
        result[i] = sorted(result[i].items(), key = lambda x: x[1], reverse=True)
    
    #2개까지 출력
    for i in range(len(genres_set)):
        if len(result[i]) <= 1:
            answer.append(result[i][0][0])
        else:
            answer.append(result[i][0][0])
            answer.append(result[i][1][0])

    return answer
```

29\. 디스크 컨트롤러 (Level 3)
[42627](https://school.programmers.co.kr/learn/courses/30/lessons/42627) 
```python
from heapq import heappush, heappop

def solution(jobs):
    answer = []
    l = len(jobs)
    time = 0
    waiting = []
    
    jobs.sort()

    while True:
        #time안에 실행할 수 있는 모든 작업을 waiting에 넣기
        while True:
            if jobs and jobs[0][0] <= time:
                heappush(waiting, (jobs[0][1], jobs[0][0])) #2열 기준으로 정렬됨
                heappop(jobs)
            else:
                break
        
        #waiting배열이 있으면 작업시간 제일 작은 거 answer에 넣기
        if waiting:
            answer.append(time-waiting[0][1]+waiting[0][0])
            time += waiting[0][0]
            heappop(waiting)
        else:
            time += 1
            
        #answer에 값이 다 들어가면 종료    
        if len(answer) == l:
            break
        
    return sum(answer)//len(answer)
```

30\. 이중우선순위큐 (Level 3)
[42628](https://school.programmers.co.kr/learn/courses/30/lessons/42628)  
31\. 섬 연결하기 (Level 3)
[42861](https://school.programmers.co.kr/learn/courses/30/lessons/42861)  
32\. 단속카메라 (Level 3)
[42884](https://school.programmers.co.kr/learn/courses/30/lessons/42884)  
```python
def solution(routes):
    routes = sorted(routes, key=lambda x:(x[1], x[0]))

    camera = routes[0][1]
    answer = 1
    for i in range(len(routes)):
        if camera < routes[i][0]:
            camera = routes[i][1]
            answer += 1
    return answer
```

33\. N으로 표현 (Level 3)
[42895](https://school.programmers.co.kr/learn/courses/30/lessons/42895)  
34\. 정수 삼각형 (Level 3)
[43105](https://school.programmers.co.kr/learn/courses/30/lessons/43105)  
35\. 등굣길 (Level 3)
[42898](https://school.programmers.co.kr/learn/courses/30/lessons/42898)  
36\. 네트워크 (Level 3)
[43162](https://school.programmers.co.kr/learn/courses/30/lessons/43162)
37\. 단어 변환 (Level 3)
[43163](https://school.programmers.co.kr/learn/courses/30/lessons/43163)
38\. 아이템 줍기 (Level 3)
[87694](https://school.programmers.co.kr/learn/courses/30/lessons/87694)
39\. 여행경로 (Level 3)
[43164](https://school.programmers.co.kr/learn/courses/30/lessons/43164)
40\. 퍼즐 조각 채우기 (Level 3)
[84021](https://school.programmers.co.kr/learn/courses/30/lessons/84021)
41\. 입국심사 (Level 3)
[43238](https://school.programmers.co.kr/learn/courses/30/lessons/43238)
42\. 가장 먼 노드 (Level 3)
[49189](https://school.programmers.co.kr/learn/courses/30/lessons/49189)
43\. 순위 (Level 3)
[49191](https://school.programmers.co.kr/learn/courses/30/lessons/49191)
44\. 사칙연산 (Level 4)
[1843](https://school.programmers.co.kr/learn/courses/30/lessons/1843)
45\. 도둑질 (Level 4)
[42897](https://school.programmers.co.kr/learn/courses/30/lessons/42897)
46\. 징검다리 (Level 4)
[43236](https://school.programmers.co.kr/learn/courses/30/lessons/43236)
47\. 방의 개수 (Level 5)
[49190](https://school.programmers.co.kr/learn/courses/30/lessons/49190)