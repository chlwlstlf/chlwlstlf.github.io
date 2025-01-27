---
layout: single
title: "[정보처리기사] 1단원"
categories: elp
toc: true
toc_sticky: true
---

# I 요구사항 확인

# <mark style='background-color: #fed3fe'>01 소프트웨어 개발 방법론</mark>

## <mark style='background-color: #fdb5bd'>1. 소프트웨어 개발 방법론</mark>

### <mark class="pink">(1) 소프트웨어 생명주기 모델</mark>

1\. <mark class="yellow">소프트웨어 생명주기(SDLC)</mark>

- Software Development Life Cycle
- 시스템의 요구분석부터 유지보수까지 전 공정을 체계화한 절차

2\. 소프트웨어 생명주기 모델 프로세스

1. 요구사항 분석
2. 설계
3. 구현
4. 테스트
5. 유지보수

3\. 소프트웨어 생명주기 모델 종류

- 폭포수 모델 (확실히 마무리, 선형 순차)
- 프로토타이핑 모델 (피드백)
- 나선형 모델 (점진적, <mark class="yellow">위험 감소</mark>)
- 반복적 모델 (일부분을 반복적)

### <mark class="pink">(2) 소프트웨어 개발 방법론</mark>

1\. 소프트웨어 개발 방법론  
소프트웨어 개발 전 과정에서 지속적으로 적용할 수 있는 방법, 절차, 기법

2\. 소프트웨어 개발 방법론 종류

- 구조적 방법론 (분할과 정복)
- 정보공학 방법론 (대형 프로젝트)
- 객체 지향 방법론 (객체)
- 컴포넌트 기반 방법론 (확장성, 재사용)
- <mark class="yellow">애자일 방법론 (유연하고 빠르게 개발하는 방법론)</mark>
- 제품 계열 방법론 (공통된 기능)

3\. 애자일 방법론

- <mark class="yellow">XP</mark>
  - 5가지 가치(용기, 단순성, 의사소통, 피드백, 존중)
  - 12가지 기본원리(짝 프로그래밍, 공동 코드 소유, 지속적인 통합, 계획 세우기, 작은 릴리즈, 메타포어, 간단한 디자인, <mark class="yellow">테스트 기반 개발(TDD)</mark>, <mark class="yellow">리팩토링</mark>, 40시간 작업, 고객 상주, 코드 표준)
- 스크럼
- 린

### <mark class="pink">(3) 객체 지향 분석 방법론</mark>

1\. 객체 지향  
실세계의 개체를 속성과 메서드가 결합한 형태의 객체로 표현

2\. 객체 지향 구성요소  
<mark class="yellow">클래스</mark>, 객체, 메서드, 메시지, 인스턴스, 속성

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

## <mark style='background-color: #fdb5bd'>2. 프로젝트 관리</mark>

### <mark class="pink">(1) 프로젝트 관리</mark>

1\. 프로젝트 관리  
주어진 기간 내에 최소의 비용으로 사용자를 만족시키는 시스템을 개발하기 위한 전반적인 활동

### <mark class="pink">(2) 비용산정 모형</mark>

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

### <mark class="pink">(3) 일정관리 모델</mark>

2\. 일정관리 모델 분류

- 주 공정법(CPM): 가장 긴 시간 경로
- PERT: 비관치, 중간치, 낙관치
- 중요 연쇄 프로젝트 관리(CCPM)

### <mark class="pink">(4) 위험 관리</mark>

3\. 위험 대응 전략  
회피, 전가, 완화, 수용

# <mark style='background-color: #fed3fe'>02 현행 시스템 분석</mark>

## <mark style='background-color: #fdb5bd'>1. 현행 시스템 파악</mark>

### <mark class="pink">(1) 현행 시스템 파악 개념</mark>

어떤 하위 시스템으로 구성되어 있고, 제공 기능 및 연계 정보는 무엇이며 어떤 기술 요소를 사용하는지를 파악하는 활동

### <mark class="pink">(2) 현행 시스템 파악 절차</mark>

1. 1단계: 구성/기능/인터페이스 파악
2. 2단계: 아키텍처 및 소프트웨어 구성 파악
3. 3단계: 하드웨어 및 네트워크 구성 파악

### <mark class="pink">(3) 소프트웨어 아키텍처</mark>

2\. 소프트웨어 아키텍처 프레임워크 구성요소

- 아키텍처 명세서
- 이해관계자
- 관심사
- 관점

<mark class="yellow">3. 소프트웨어 아키텍처 4+1 뷰</mark>

- 4
  - 논리 뷰: 설계자, 개발자 관점
  - 프로세스 뷰: 개발자, 시스템 통합자 관점
  - 구현 뷰: 모듈의 구성
  - 배포 뷰: 물리, 배치
- 1
  - 유스케이스 뷰: 사용자, 설계자, 개발자, 테스트 관점

4\. 소프트웨어 아키텍처 패턴  
유형

- 계층화 패턴: 두 개의 계층 사이에서만 상호 작용
- 클라이언트-서버 패턴: 하나의 서버, 다수의 클라
- 파이프-필터: 서브 시스템에 넘겨줌
- 브로커 패턴: 분산 시스템
- 모델-뷰-컨트롤러 패턴: 각 부분이 별도의 컴포넌트

### <mark class="pink">(4) 디자인 패턴</mark>

<mark class="yellow">1. 목적</mark>

- 생성
  - Builder: 조립
  - Prototype: 일반적인 원형
  - Factory Method: 상위 클래스 정의, 서브 클래스 생성
  - Abstract Factory: 구체적인 클래스x
  - Singleton: 전역 변수x
- 구조
  - Bridge: 클래스, 구현 연결
  - Decorator
  - Facade
  - Flyweight
  - Proxy
  - Composite
  - Adapter
- 행위
  - Template Method: 일부분을 서브 클래스로 캡슐화
  - Command: 실행될 기능을 캡슐화
  - Observer: 느슨하게
  - State: 객체 상태를 캡슐화
  - Strategy: 알고리즘 군 정의
  - Mediator
  - Interpreter
  - Iterator
  - Visitor
  - Memento
  - Chain of Responsibility

2\. 범위

- 클래스
- 객체

## <mark style='background-color: #fdb5bd'>2. 개발 기술 환경 정의</mark>

### <mark class="pink">(1) 개발 기술 환경 현행 시스템 분석</mark>

1\. 운영체제 종류 및 특징

- PC
  - 윈도즈
  - 유닉스
  - 리눅스
- 모바일
  - 안드로이드
  - iOS

2\. OSI 7계층

- 응용 계층: 데이터
- 표현 계층: 데이터
- 세션 계층: 데이터
- 전송 계층: 세그먼트
- 네트워크 계층: 패킷
- 데이터 링크 계층: 프레임
- 물리 계층: 비트

# <mark style='background-color: #fed3fe'>03 요구사항 확인</mark>

## <mark style='background-color: #fdb5bd'>1. 요구사항</mark>

### <mark class="pink">(1) 요구사항 개념</mark>

3\. 요구사항의 분류

- 기능적  
  기능, 서비스에 대한 요구사항

- 비기능적 요구사항  
  시스템 구축에 대한 제약사항(서버 계속 가동)

### <mark class="pink">(2) 요구공학 프로세스</mark>

1\. 요구사항 개발 단계 구성  
도출 -> 분석 -> 명세 -> 확인 및 검증

2\. 요구사항 개발 단계 상세

3\. 명세 단계

- 비정형 명세 기법: 자연어
- 정형 명세 기법: 수학적

4\. 확인 및 검증 단계

- 동료 검토: 2~3명
- 워크 스루: 회의
- 인스펙션: 전문가 or 팀
