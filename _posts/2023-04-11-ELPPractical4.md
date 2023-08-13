---
layout: archive
title: "정보처리기사 4단원"
categories: elp
toc: true
toc_sticky: true
---

# IV 통합 구현

---

# <mark style='background-color: #fed3fe'>01 연계 메커니즘 구성</mark>

## <mark style='background-color: #fdb5bd'>1. 연계 메커니즘 정의</mark>

### <mark style='background-color: #ffdce0'>(1) 연계 메커니즘 개념</mark>

1\. 연계 방식

- 직접 연계 방식
  - 장점: 단순, 기간 짧음, 성능 좋음
  - 단점: 변경 민감
  - 기술: DB 링크, DB 연결, API, JDBC, 하이퍼 링크
- 간접 연계 방식
  - 장점: 통합, 오류 없음, 자유롭게 반영
  - 단점: 성능 저하, 장기간
  - 기술: 연계 솔루션, Web Service, 소켓

# <mark style='background-color: #fed3fe'>02 내외부 연계 모듈 구현</mark>

### <mark style='background-color: #ffdce0'>(2) EAI</mark>

1\. 개념
기업에서 운영되는 서로 다른 플랫폼 및 애플리케이션 간의 정보를 전달, 연계, 통합이 가능하도록 해주는 솔루션

2\. 구성요소

- EAI 플랫폼: 이기종 시스템 간 상호 운영
- 어댑터: 입출력 도구
- 브로커: 포맷과 코드 변환
- 메시지 큐: 다른 응용 프로그램 사이에서 데이터 송수신
- 비즈니스 워크플로우

3\. 구축 유형

- 포인트 투 포인트: 1:1 단순 통합방법, 커뮤니케이션
- 허브 앤 스포크: 허브 시스템, 중앙 집중식
- 메시지 버스: 미들웨어 통합, 확장성, 대용량 처리
- 하이브리드: 그룹 내-허브 앤 스포크 / 그룹 간-메시지 버스

### <mark style='background-color: #ffdce0'>(4) 웹 서비스 방식</mark>

1\. 용어

- HTTP: 요청과 응답에 의해 처리하는 프로토콜
- 하이퍼텍스트: 네트워크, 하이퍼링크
- HTML: 기초적인 구성요소, 마크업 언어

<mark style='background-color: #fff5b1'>2. 유형</mark>

- SOAP: HTTP, HTTPS, SMTP 등을 사용하여 XML 기반의 메시지를 네트워크 상태에서 교환하는 프로토콜
- WSDL: 웹 서비스에 대한 상세 정보가 기술된 XML 형식으로 구현되어 있는 언어
- UDDI: 웹 서비스에 대한 정보인 WSDL을 등록하고 검색하기 위한 저장소로 공개적으로 접근, 검색이 가능한 레지스트리이자 표준

### <mark style='background-color: #ffdce0'>(5) IPC 방식</mark>

주요 기법

- 메시지 큐: 프로세스 간 통신
- 공유메모리:한 프로세스 일부분을 공유
- 소켓: 클라이언트와 서버 프로세스 간 통신
- 세마포어: 프로세스 동기 맞춤
