---
layout: single
title:  "정보처리기사-실기1"
categories: Certificate
toc: true
toc_sticky: true
---

# 정보처리기사 실기 Vol.1

# <mark style='background-color: #fca1ab'># I 요구사항 확인</mark>

# <mark style='background-color: ##fdb5bd'>01 소프트웨어 개발 방법론</mark>

## <mark style='background-color: #f9c7cd'>1. 소프트웨어 개발 방법론</mark>

### <mark style='background-color: #ffdce0'>(1) 소프트웨어 생명주기 모델</mark>

1\. <mark style='background-color: #fff5b1'>소프트웨어 생명주기(SDLC)</mark>  
- Software Development Life Cycle
- 시스템의 요구분석부터 유지보수까지 전 공정을 체계화한 절차

2\. 소프트웨어 생명주기 모델 프로세스   
1) 요구사항 분석  
2) 설계  
3) 구현  
4) 테스트  
5) 유지보수  

3\. 소프트웨어 생명주기 모델 종류
- 폭포수 모델 (확실히 마무리, 선형 순차)
- 프로토타이핑 모델 (피드백)
- 나선형 모델 (점진적, <mark style='background-color: #fff5b1'>위험 감소</mark>)
- 반복적 모델 (일부분을 반복적)

### <mark style='background-color: #ffdce0'>(2) 소프트웨어 개발 방법론</mark>

1\. 소프트웨어 개발 방법론  
소프트웨어 개발 전 과정에서 지속적으로 적용할 수 있는 방법, 절차, 기법

2\. 소프트웨어 개발 방법론 종류
- 구조적 방법론 (분할과 정복)
- 정보공학 방법론 (대형 프로젝트)
- 객체 지향 방법론 (객체)
- 컴포넌트 기반 방법론 (확장성, 재사용)
- <mark style='background-color: #fff5b1'>애자일 방법론 (유연하고 빠르게 개발하는 방법론)</mark>
- 제품 계열 방법론 (공통된 기능)

3\. 애자일 방법론
- <mark style='background-color: #fff5b1'>XP</mark>
  - 5가지 가치(용기, 단순성, 의사소통, 피드백, 존중)
  - 12가지 기본원리(짝 프로그래밍, 공동 코드 소유, 지속적인 통합, 계획 세우기, 작은 릴리즈, 메타포어, 간단한 디자인, <mark style='background-color: #fff5b1'>테스트 기반 개발(TDD)</mark>, <mark style='background-color: #fff5b1'>리팩토링</mark>, 40시간 작업, 고객 상주, 코드 표준)
- 스크럼
- 린

### <mark style='background-color: #ffdce0'>(3) 객체 지향 분석 방법론</mark>

1\. 객체 지향  
실세계의 개체를 속성과 메서드가 결합한 형태의 객체로 표현

2\. 객체 지향 구성요소  
<mark style='background-color: #fff5b1'>클래스</mark>, 객체, 메서드, 메시지, 인스턴스, 속성

3\. 객체 지향 기법
- 캡슐화 (결합도 낮아짐, 재사용, 단순화)
- 상속성
- 다형성 (오버로딩: 같은 이름 다른 메서드, 오버라이딩: 재정의)
- 추상화 (공통 성질 추출)
- 정보 은닉 
- 관계성

4\. 객체 지향 설계 원칙(SOLID)
- 단일 책임의 원칙(SRP): 하나의 클래스는 하나의 목적
- 개방 폐쇄 원칙(OCP): 확장에는 열림, 변경에는 닫힘
- 리스코프 치환의 원칙(LSP): 상위로 교체 가능
- 인터페이스 분리의 원칙(ISP): 인터페이스 영향x
- 의존성 역전의 원칙(DIP): 추상, 관계 느슨

5\. 객체 지향 분석  
사용자의 요구사항을 분석하여 요구된 문제와 관련된 모든 클래스, 속성과 연산, 관계를 정의하여 모델링하는 기법


6\. 객체 지향 분석 방법론 종류
럼바우: 객체 모델링(정보 모델링, 객체D) -> 동적 모델링(상태D) -> 기능 모델링(자료 흐름도)

## <mark style='background-color: #f9c7cd'>2. 프로젝트 관리</mark>

### <mark style='background-color: #ffdce0'>(1) 프로젝트 관리</mark>


1\. 프로젝트 관리  
주어진 기간 내에 최소의 비용으로 사용자를 만족시키는 시스템을 개발하기 위한 전반적인 활동

### <mark style='background-color: #ffdce0'>(2) 비용산정 모형</mark>

2\. 비용산정 모형 분류
- 하향식
  - 전문가 판단
  - 델파이 기법
- 상향식
  - LoC: 낙관치, 중간치, 비관치
  - Man Month: LoC/월간 생산성
  - COCOMO: 조직형, 반 분리형, 임베디드형
  - 푸트남
  - 기능점수(FP): 가중치

### <mark style='background-color: #ffdce0'>(3) 일정관리 모델</mark>

2\. 일정관리 모델 분류
- 주 공정법(CPM): 가장 긴 시간 경로
- PERT: 비관치, 중간치, 낙관치
- 중요 연쇄 프로젝트 관리(CCPM)

### <mark style='background-color: #ffdce0'>(4) 위험 관리</mark>

3\. 위험 대응 전략  
회피, 전가, 완화, 수용

