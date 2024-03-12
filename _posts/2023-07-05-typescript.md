---
layout: single
title: "TypeScript 시작하기"
categories: React
toc: true
toc_sticky: true
---

# 타입스크립트 설치, 문법

## 1. 설치

1. nodejs 설치
2. VScode 에디터 준비
3. npm install -g typescript
4. index.ts, tsconfig.json 파일 만들기
5. tsconfig.json에 아래 코드 복붙하기

```tsx
{
  "compilerOptions" : {
    "target": "es5",
    "module": "commonjs",
  }
}
```

6\. tsc -w : ts 파일을 js파일로 자동 변환

---

## 2. 문법

1\. 간단한 변수 타입 지정 가능  
변수: string, number, boolean, null, undefined, bigint, [], {} 등

```tsx
let 이름: string = "kim"; //문자만 가능
let 이름: string[] = ["kim", "park"]; //string 담긴 array만
let 이름: { name?: string } = { name: "kim" }; //name? → name이어도 되고 아니어도 됨
let 이름: string | number = "kim";
```

2\. 타입을 변수에 담아서 쓰기

```tsx
type 내타입 = string | number;
let 이름: 내타입 = 123;
```

3\. 함수에 타입 지정 가능

```tsx
//파라미터 number, 리턴값 number
function 함수(x: number): number {
  return x * 2;
}
```

4\. array에 쓸 수 있는 tuple 타입

```tsx
type Member = [number, boolean];
let john: Member = [123, true];
```

5\. object에 타입 지정해야할 속성이 너무 많으면

```tsx
type Member = {
  [key: string]: string;
};
let john: Member = { name: "kim" };
```

6\. class 타입 지정 가능

```tsx
class User {
  name: string;
  constructor(name: str) {
    this.name = name;
  }
}
```
