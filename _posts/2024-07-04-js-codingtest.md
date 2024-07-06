---
layout: single
title: "[JS] 코딩테스트 준비"
categories: cote
toc: true
toc_sticky: true
---

# 자바스크립트로 코딩테스트 준비하기

## fs 모듈 사용

```js
const fs = require("fs");
const input = fs.readFileSync("/dev/stdin");
```

<br>

<mark style='background-color: #ffdce0'>1-1. 입력값이 한 개일 때(문자)</mark>

```js
const input = fs.readFileSync("/dev/stdin").toString().trim();

//input: hello
//output: hello
```

<br>

<mark style='background-color: #ffdce0'>1-2. 입력값이 한 개일 때(숫자)</mark>

```js
const input = +fs.readFileSync("/dev/stdin").toString().trim();

//input: 8
//output: 8
```

<br>

<mark style='background-color: #ffdce0'>2-1. 입력값이 띄어쓰기로 구분된 한 줄의 값들인 경우(문자)</mark>

```js
const input = fs.readFileSync("/dev/stdin").toString().trim().split(" ");

//input: hello world
//output: ['hello', 'world']
```

<br>

<mark style='background-color: #ffdce0'>2-2. 입력값이 띄어쓰기로 구분된 한 줄의 값들인 경우(숫자)</mark>

```js
const input = fs
  .readFileSync("/dev/stdin")
  .toString()
  .trim()
  .split(" ")
  .map(Number);

//input: 8 7 56
//output: [8, 7, 56]
```

<br>

<mark style='background-color: #ffdce0'>3-1. 입력값이 여러 줄의 값들인 경우(문자)</mark>

```js
const input = fs.readFileSync("/dev/stdin").toString().trim().split("\n");

//input:
//a
//b
//c
//d
//output: ['a', 'b', 'c', 'd']
```

<br>

<mark style='background-color: #ffdce0'>3-2. 입력값이 여러 줄의 값들인 경우(숫자)</mark>

```js
const input = fs
  .readFileSync("/dev/stdin")
  .toString()
  .trim()
  .split("\n")
  .map(Number);

//input:
//1
//2
//3
//4
//5
//output: [1, 2, 3, 4, 5]
```

<br>

<mark style='background-color: #ffdce0'>4-1. 입력값이 여러 줄의 값들이 띄어쓰기로 구분되어 있는 경우(문자)</mark>

```js
const input = fs
  .readFileSync("/dev/stdin")
  .toString()
  .trim()
  .split("\n")
  .map((el) => el.split(""));

//input:
//ab cd
//ef gh
//my name is jinsil
//hi hello
//output: [
//  [ 'ab', 'cd' ],
//  [ 'ef', 'gh' ],
//  [ 'my', 'name', 'is', 'jinsil' ],
//  [ 'hi', 'hello' ]
//]
```

<br>

<mark style='background-color: #ffdce0'>4-2. 입력값이 여러 줄의 값들이 띄어쓰기로 구분되어 있는 경우(모두 숫자)</mark>

```js
const input = fs
  .readFileSync("/dev/stdin")
  .toString()
  .trim()
  .split("\n")
  .map((el) => el.split("").map(Number));

//input:
//3
//1 2
//3 4 5 6
//5 3 2 5
//0 1 1 0
//output: [ [ 3 ], [ 1, 2 ], [ 3, 4, 5, 6 ], [ 5, 3, 2, 5 ], [ 0, 1, 1, 0 ] ]
```
