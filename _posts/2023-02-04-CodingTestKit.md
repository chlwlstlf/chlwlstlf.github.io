---
layout: single
title:  "[Programmers] 코딩테스트 고득점 Kit"
categories: CodingTest
toc: true
toc_sticky: true
---

# Coding Test Study 3

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
10\. 기능 개발 (Level 2)
[42586](https://school.programmers.co.kr/learn/courses/30/lessons/42586)
11\. 올바른 괄호 (Level 2)
https://school.programmers.co.kr/learn/courses/30/lessons/12909
12\. 프린터 (Level 2)
https://school.programmers.co.kr/learn/courses/30/lessons/42587
13\. 다리를 지나는 트럭 (Level 2)
https://school.programmers.co.kr/learn/courses/30/lessons/42583
14\. 주식가격 (Level 2)
https://school.programmers.co.kr/learn/courses/30/lessons/42584
15\. 더 맵게 (Level 2)
https://school.programmers.co.kr/learn/courses/30/lessons/42626
16\. 가장 큰 수 (Level 2)
https://school.programmers.co.kr/learn/courses/30/lessons/42746
17\. H-Index (Level 2)
https://school.programmers.co.kr/learn/courses/30/lessons/42747
18\. 소수 찾기 (Level 2)
https://school.programmers.co.kr/learn/courses/30/lessons/42839
19\. 카펫 (Level 2)
https://school.programmers.co.kr/learn/courses/30/lessons/42842
20\. 피로도 (Level 2)
https://school.programmers.co.kr/learn/courses/30/lessons/87946
21\. 전력망을 둘로 나누기 (Level 2)
https://school.programmers.co.kr/learn/courses/30/lessons/86971
22\. 모음사전 (Level 2)
https://school.programmers.co.kr/learn/courses/30/lessons/84512
23\. 조이스틱 (Level 2)
https://school.programmers.co.kr/learn/courses/30/lessons/42860
24\. 큰 수 만들기 (Level 2)
https://school.programmers.co.kr/learn/courses/30/lessons/42883
25\. 구명보트 (Level 2)
https://school.programmers.co.kr/learn/courses/30/lessons/42885
26\. 타겟 넘버 (Level 2)
https://school.programmers.co.kr/learn/courses/30/lessons/43165
27\. 게임 맵 최단거리 (Level 2)
https://school.programmers.co.kr/learn/courses/30/lessons/1844
28\. 베스트앨범 (Level 3)
https://school.programmers.co.kr/learn/courses/30/lessons/42579
29\. 디스크 컨트롤러 (Level 3)
https://school.programmers.co.kr/learn/courses/30/lessons/42627
30\. 이중우선순위큐 (Level 3)
https://school.programmers.co.kr/learn/courses/30/lessons/42628
31\. 섬 연결하기 (Level 3)
https://school.programmers.co.kr/learn/courses/30/lessons/42861
32\. 단속카메라 (Level 3)
https://school.programmers.co.kr/learn/courses/30/lessons/42884
33\. N으로 표현 (Level 3)
https://school.programmers.co.kr/learn/courses/30/lessons/42895
34\. 정수 삼각형 (Level 3)
https://school.programmers.co.kr/learn/courses/30/lessons/43105
35\. 등굣길 (Level 3)
https://school.programmers.co.kr/learn/courses/30/lessons/42898
36\. 네트워크 (Level 3)
https://school.programmers.co.kr/learn/courses/30/lessons/43162
37\. 단어 변환 (Level 3)
https://school.programmers.co.kr/learn/courses/30/lessons/43163
38\. 아이템 줍기 (Level 3)
https://school.programmers.co.kr/learn/courses/30/lessons/87694
39\. 여행경로 (Level 3)
https://school.programmers.co.kr/learn/courses/30/lessons/43164
40\. 퍼즐 조각 채우기 (Level 3)
https://school.programmers.co.kr/learn/courses/30/lessons/84021
41\. 입국심사 (Level 3)
https://school.programmers.co.kr/learn/courses/30/lessons/43238
42\. 가장 먼 노드 (Level 3)
https://school.programmers.co.kr/learn/courses/30/lessons/49189
43\. 순위 (Level 3)
https://school.programmers.co.kr/learn/courses/30/lessons/49191
44\. 사칙연산 (Level 4)
https://school.programmers.co.kr/learn/courses/30/lessons/1843
45\. 도둑질 (Level 4)
https://school.programmers.co.kr/learn/courses/30/lessons/42897
46\. 징검다리 (Level 4)
https://school.programmers.co.kr/learn/courses/30/lessons/43236
47\. 방의 개수 (Level 5)
https://school.programmers.co.kr/learn/courses/30/lessons/49190