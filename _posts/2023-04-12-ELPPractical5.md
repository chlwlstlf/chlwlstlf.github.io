---
layout: single
title: "[정보처리기사] 5단원"
categories: elp
toc: true
toc_sticky: true
---

# V 인터페이스 구현

---

# <mark style='background-color: #fed3fe'>01 인터페이스 설계 확인</mark>

## <mark style='background-color: #fdb5bd'>1. 외부 및 내부 모듈 연계를 위한 인터페이스 기능 식별</mark>

### <mark class="pink">(1) EAI, ESB 연계 방법</mark>

1\. EAI 방식: 정보를 전달, 연계, 통합이 가능하도록 해주는 솔루션

- 포인트 투 포인트: 1:1, 단순 통합
- 허브 앤 스포트: 허브 시스템, 중앙 집중식
- 메시지 버스: 미들웨어 통합 방식
- 하이브리드: 그룹 내-허브 앤 스포크 / 그룹 간-메시지 버스

2\. ESB 방식: 하나의 시스템으로 관리 운영할 수 있도록 서비스 중심의 통합을 지향하는 아키텍처, 느슨한 결합 방식

# <mark style='background-color: #fed3fe'>02 인터페이스 기능 구현</mark>

## <mark style='background-color: #fdb5bd'>1. 인터페이스 기능 구현</mark>

### <mark class="pink">(1) 인터페이스 기능 구현 기술</mark>

<mark class="yellow">1. JSON(JavaScript Object Notation)</mark>

1\) 개념

- 속성-값 쌍 or 키-값 쌍
- 데이터 오브젝트를 전달하기 위해 인간이 읽을 수 있는 텍스트를 사용하는 개방형 표준 포맷

2\) 특징

- AJAX에서 많이 사용되고 XML을 대체하는 주요 데이터 포맷

<mark class="yellow">2. XML(eXtensible Markup Language)</mark>

1\) 개념

- HTML의 단점을 보완한 인터넷 언어
- SGML의 복잡한 단점을 개선한 특수한 목적을 갖는 마크업 언어

2\) 특징

- 송, 수신 시스템 간 데이터 연계의 편의성을 위해서 전송되는 데이터 구조를 동일한 형태로 정의

<mark class="yellow">3. AJAX(Asynchronous Javascript And Xml)</mark>

1\) 개념

- 자바스크립트를 사용하여 웹 서버와 클라이언트 간 비동기적으로 XML 데이터를 교환하고 조작하기 위한 웹 기술

2\) 주요기술  
 XMLHttpRequest, JavaScript, XML, DOM, XSLT, HTML, CSS

4\. REST

1\) 개념

- HTTP 메서드로 주고받는 웹 아키텍처

## <mark style='background-color: #fdb5bd'>2. 인터페이스 보안 기능 적용</mark>

### <mark class="pink">(2) 인터페이스 보안 구현 방안</mark>

1\. 시큐어 코딩 가이드

- 입력데이터 검증 및 표현: 입력값 검증 -> 유효성 검증
- 보안 기능: 보안 기능 부적절 -> 정책 반영되게 구현
- 시간 및 상태: 동시 수행 관리 부적절 -> 공유 자원 접근 직렬화
- 에러 처리: 에러 미처리 -> 에러 상황 처리
- 코드 오류: 코딩 오류 -> 경고 메시지 코드 제거
- 캡슐화: 데이터 누출 -> 프라이빗 접근자
- API 오용: 반하는 방법으로 API 호출 -> 취약 API 검출 프로그램 사용

2\. 데이터 베이스 보안 적용

1\) 암호화 알고리즘

- 대칭 키 암호화 알고리즘: 암, 복호화에 같은 암호 키 씀
- 비대칭 키 암호화 알고리즘: 공개키, 비밀키
- 해시 암호화 알고리즘: 해시값으로 원래 입력값 찾음

2\) 암호화 기법

- API 방식: 애플리케이션 서버
- Plug-in 방식: DB 서버
- TDE 방식: DB 서버의 DBMS 커널
- Hybrid 방식: API + Plug-in

# <mark style='background-color: #fed3fe'>03 인터페이스 구현 검증</mark>

### <mark class="pink">(2) 인터페이스 구현 검증 도구</mark>

- xUnit: 다양한 언어를 지원하는 단위테스트 프레임워크
- STAF: 서비스 호출, 컴포넌트 재사용 등 다양한 환경을 지원하는 테스트 프레임워크
- FitNesse: 사용자가 테이트 케이스 테이블을 작성하면 빠르고 편하게 자동으로 원하는 값에 대해 테스트
- NTAF: FitNesse-협업 + STAF-재사용,확장성
- Seleninum: 개발언어를 지원하는 웹 애플리케이션 테스트 프레임워크
- watir: 루비 기반 웹 애플리케이션 테스트 프레임워크
