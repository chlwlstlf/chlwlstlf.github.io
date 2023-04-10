---
layout: single
title:  "정보처리기사 3단원"
categories: Certificate
toc: true
toc_sticky: true
---

# II 데이터 입출력 구현

# <mark style='background-color: #fed3fe'>01 논리 데이터 저장소 확인</mark>
> 모든 항목들이 중요 개념

## <mark style='background-color: #fdb5bd'>1. 데이터 모델</mark>

### <mark style='background-color: #ffdce0'>(1) 데이터 모델 개념</mark>

현실 세계의 정보를 인간과 컴퓨터가 이해할 수 있도록 추상화하여 표현한 모델

<pre id="code_1642267924240" class="shell" data-ke-language="shell" data-ke-type="codeblock">
  <code class="hljs">
    <p>데이터 모델 표시 요소</p>
    <div>연산: 실제 데이터를 처리하는 작업에 대한 명세</div>
    <div>구조: 논리적으로 표현될 대상</div>
    <div>제약 조건: 무결성 유지</div>
  </code>
</pre>

### <mark style='background-color: #ffdce0'>(2) 데이터 모델 절차</mark>
1) 요구사항 분석: 데이터에 대한 요구 분석

2) 개념적 데이터 모델: 트랜잭션 모델링, 개체관계 다이어그램

3) 논리적 데이터 모델: 트랜잭신의 인터페이스 설계, 정규화

4) 물리적 데이터 모델: 객체 생성, 반 정규화, 레코드

## <mark style='background-color: #fdb5bd'>2. 논리 데이터 모델 검증</mark>

### <mark style='background-color: #ffdce0'>(1) 논리 데이터 모델링 개념</mark>

업무의 모습을 모델링 표기법으로 형상화하여 사람이 이해하기 쉽게 표현하는 프로세스

### <mark style='background-color: #ffdce0'>(2) 논리 데이터 모델링 종류</mark>

- 관계 데이터 모델: 2차원 테이블, PK/FK
- 계층 데이터 모델: 트리 형태, 부모-자식, 1:N만
- 네트워크 데이터 모델: 그래프 형태, N:M

### <mark style='background-color: #ffdce0'>(3) 관계 데이터 모델</mark>
>중요!!

1\. 관계 데이터 모델
1) 개념: 데이터를 행과 열로 구성된 2차원 테이블 형태로 구성한 모델

<mark style='background-color: #fff5b1'>2) 구성요소</mark> 

- 릴레이션: 테이블
- 튜플: 행
- 속성: 열
- 카티널리티: 튜플의 수(행의 수)
- 차수: 속성의 수(열의 수)
- 스키마: 데베 구조, 제약 조건 담고 있음
- 인스턴스: 실제 저장된 데이터의 집합

2\. 관계 대수
1) 개념: 관계형 데이터베이스에서 원하는 정보과 그 정보를 어떻게 유도하는가를 기술하는 절차적 정형 언어

<mark style='background-color: #fff5b1'>2) 관계 대수 연산자의 종류</mark>

- 일반 집합 연산자: 합집합, 교집합, 차집합, 카티션 프로덕트
- 순수 관계 연산자: 셀렉트, 프로젝트, 조인, 디비전

3\. 관계 해석

1) 개념: 튜플 관계 해석과 도메인 관계 해석을 하는 비절차적 언어

### <mark style='background-color: #ffdce0'>(4) 논리 데이터 모델링 속성</mark>




# <mark style='background-color: #fed3fe'>02 물리 데이터 저장소 설계</mark>



# <mark style='background-color: #fed3fe'>03 데이터베이스 기초 활용하기</mark>