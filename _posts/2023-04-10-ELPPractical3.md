---
layout: single
title:  "정보처리기사 3단원"
categories: Certificate
toc: true
toc_sticky: true
---

# II 데이터 입출력 구현

***

# <mark style='background-color: #fed3fe'>01 논리 데이터 저장소 확인</mark>
> 모든 항목들이 중요 개념

## <mark style='background-color: #fdb5bd'>1. 데이터 모델</mark>

### <mark style='background-color: #ffdce0'>(1) 데이터 모델 개념</mark>

현실 세계의 정보를 인간과 컴퓨터가 이해할 수 있도록 추상화하여 표현한 모델  

데이터 모델 표시 요소
- 연산: 실제 데이터를 처리하는 작업에 대한 명세
- 구조: 논리적으로 표현될 대상
- 제약 조건: 무결성 유지

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

1\. 개체: 관리할 대상이 되는 실체(□)

2\. 속성: 관리할 정보의 구체적 항목(○)

3\. 관계: 개체 간의 대응 관계(---, >-<)

### <mark style='background-color: #ffdce0'>(5) 개체-관계(E-R) 모델</mark>

<mark style='background-color: #fff5b1'>2) 기호</mark>
- 개체 집합: □
- 관계 집합: ◇
- 속성: ○
- 다중 값 속성: ◎
- 개체 집합-관계 집합 연결: ---
- 개체 집합-속성 연결: ---
- 관계 집합-속성 연결: - - -

### <mark style='background-color: #ffdce0'>(6) 정규화</mark>

1\. 개념  
중복성 제거, 이상 현상 방지, 일관성과 정확성 유지, 무손실 분해

<mark style='background-color: #fff5b1'>2. 이상 현상</mark>

데이터 중복성으로 인해 릴레이션을 조작할 떄 발생  

- 삽입 이상
- 삭제 이상
- 갱신 이상

3\. 정규화의 단계
- 1정규형: 원자값으로 구성
- 2정규형: 부분 함수 종속 제거
- 3정규형: 이행함수 종속 제거
- BCNF: 결정자 후보 키가 아닌 함수 종속 제거
- 4정규형: 다치 종속 제거
- 5정규형: 조인 종속 제거

4\. 함수 종속
- 부분 함수 종속: 기본 키가 복합 키인 경우에 종속
- 완전 함수 종속: X→Y 인 경우, Y는 X의 전체 속성에 대해 종속, 부분 집합 속성에 종속하지 않은 경우
- 이행 함수 종속: X→Y, Y→Z 일 때, X→Z 성립

### <mark style='background-color: #ffdce0'>(7) 반 정규화</mark>

- 테이블
  - 테이블 병합: 1:1, 1:M 관계를 통합, 조인 횟수 줄이기
  - 테이블 분한: 수평 분할, 수직 분할
  - 중복 테이블 추가: 집계 테이블 추가, 진행 테이블 추가, 특정 부분만을 포함하는 테이블 추가
- 컬럼: 컬럼 중복화(중복 허용)
- 관계: 중복관계 추가(추가적인 관계 맺음) 

# <mark style='background-color: #fed3fe'>02 물리 데이터 저장소 설계</mark>

## <mark style='background-color: #fdb5bd'>2. 물리 데이터 저장소 구성</mark>

### <mark style='background-color: #ffdce0'>(1) 데이터베이스 무결성</mark>

무결성 종류
- 개체 무결성: 기본 키는 NULL 안 됨, 유니크 인덱스
- 참조 무결성: 외래 키는 다른 개체의 기본 키거나 NULL
- 속성 무결성: 기본 값, NULL 여부, 도메인
- 사용자 정의 무결성
- 키 무결정: 같은 튜플 안 됨

### <mark style='background-color: #ffdce0'>(2) 키</mark>
1\. 특성: 유일성, 최소성

2\. 종류
- 기본 키: 고유하게 식별
- 대체 키: 후보 키 중 기본 키 아닌 것
- 후보 키: 기본 키 + 대체 키
- 슈퍼 키: 유일성 만족, 최소성 만족x
- 외래 키: 다른 릴레이션의 기본 키로 이용되는 키



# <mark style='background-color: #fed3fe'>03 데이터베이스 기초 활용하기</mark>