---
layout: single
title: "자료구조-재귀"
categories: C언어
toc: true
toc_sticky: true
---

# 자료구조 3주차

## 기본 규칙

  <div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
    <p>
      <div>베이스 케이스</div>
      <div>베이스 케이스를 항상 가져야 하며, 이는 재귀 없이 해결될 수 있어야 한다</div>
    </p>
    <p>
      <div>진행 방향</div>
      <div>재귀적으로 해결되어야 할 경우, 재귀호출은 항상 베이스 케이스를 향하는 방향으로 진행되어야 한다</div>
    </p>
		<p>
      <div>정상작동 가정</div>
      <div>모든 재귀호출이 제대로 작동한다고 가정하라!</div>
    </p>
		<p>
      <div>적절한 사용</div>
      <div>꼭 필요할 때만 사용하라, 저장/복구 때문에 성능이 저하되기 때문이다</div>
    </p>
  </div>

## 응용문제1 : 최대공약수 구하기 - 유클리드 호제법

입력예시  
12 8

출력예시  
4

```c
#include<stdio.h>
#include<string.h>
#include<stdlib.h>
#pragma warning(disable:4996)

int gcd(int a, int b);

int main()
{
	int x, y, max, min;
	scanf("%d %d", &x, &y);
	max = x > y ? x : y;
	min = x < y ? x : y;
	printf("%d", gcd(max, min));
}

int gcd(int a, int b)
{
	if (a == 0 || b == 0)
	{
		return a == 0 ? b : a;
	}
	else
	{
		return gcd(b, a%b);
	}
}
```

## 응용문제2 : 하노이탑

하노이탑 알고리즘 설명 영상 (4분 50초부터)
{% include video id="FYCGV6F1NuY" provider="youtube" %}

- 하노이 탑(towers of Hanoi) 문제
  - 세 개의 말뚝: A, B, C
  - 초기 상황: 직경이 다른 N>0 개의 원반이 A에 쌓여 있음
  - 목표: 모든 원반을 A로부터 C로 옮김
- 조건
  - 한 번에 말뚝의 가장 위에 있는 한 개의 원반만 이동 가능
  - 직경이 큰 원반은 작은 원반 위에 놓일 수 없음
  - 남은 말뚝을 보조 말뚝으로 사용 가능
- hanoi는 아래의 매개변수들을 사용하여 재귀적 rHanoi를 구동한다
  - n: 이동해야 할 원반 수
  - from: 출발 말뚝
  - aux: 보조 말뚝
  - to: 목표 말뚝

입력예시  
2

출력예시  
A B ↦ 말뚝 A의 맨 위 원반을 말뚝 B로 이동  
A C ↦ 이하 동일  
B C

의사코드

```
Alg hanoi(n)
1. rHanoi(n, ‘A’, ‘B’, ‘C’) {initial call}
2. return

Alg rHanoi(n, from, aux, to) {recursive}
    input integer n, peg from, aux, to
    output move sequence

1. if (n = 1) {base case}
        write(“move from”, from, “to”, to)
        return
2. rHanoi(n – 1, from, to, aux) {recursion}
3. write(“move from”, from, “to”, to)
4. rHanoi(n – 1, aux, from, to) {recursion}
5. return
```

C언어

```c
#include<stdio.h>
#include<string.h>
#include<stdlib.h>
#pragma warning(disable:4996)

void rHanoi(int n, char from, char aux, char to);

int main()
{
	int N;
	scanf("%d", &N);
	rHanoi(N, 'A', 'B', 'C');
}

void rHanoi(int n, char from, char aux, char to)
{
	if (n == 1)
	{
		printf("%c %c\n", from, to);
	}
	else
	{
		rHanoi(n - 1, from, to, aux);
		printf("%c %c\n", from, to);
		rHanoi(n - 1, aux, from, to);
	}
}
```
