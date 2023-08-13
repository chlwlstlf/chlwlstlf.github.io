---
layout: archivee
title: "정보처리기사 8단원"
categories: Certificate
toc: true
toc_sticky: true
---

# VIII 서버 프로그램 구현

---

# <mark style='background-color: #fed3fe'>01 개발환경 구축</mark>

## <mark style='background-color: #fdb5bd'>1. 개발환경 구축</mark>

### <mark style='background-color: #ffdce0'>(2) 개발 도구 분류</mark>

- 구현 도구

  - Eclipse
  - IntelliJ
  - Spring Tool Suite
  - NetBeans
  - Visual Studio

- 테스트 도구

  - xUnit
  - PMD
  - Findbugs
  - Cppcheck
  - Sonar

- 형상관리 도구

  - CVS
  - Subversion
  - Git

- 빌드 도구
  - Ant
  - Maven
  - Gradle

### <mark style='background-color: #ffdce0'>(2) 개발환경 구성요소</mark>

1\. 하드웨어 개발환경

1. 서버 하드웨어 개발환경

- 웹 서버
- 웹 애플리케이션 서버
- 데이터베이스 서버
- 파일 서버

2. 클라이언트 하드웨어 개발환경

- 클라이언트 프로그램
- 웹 브라우저
- 모바일 앱
- 모바일 웹

2\. 소프트웨어 개발환경

- 운영체제
  - Windows
  - Unix
  - Linux
- 미들웨어
  - Weblogic
  - Websphere
  - Jeus
  - Tomcat
- DBMS
  - Oracle
  - MySQL
  - MS-SQL
  - PostgreSQL

3\. 형상 관리

> 개발 전체 과정에서 발생하는 모든 항목의 변경 사항 관리

도구별 특징

- CVS: 서버/클라이언트, 동시에 접근 가능
- SVN: 서버/클라이언트, 하나의 서버, 중복 문제 해결
- RCS: 공유 폴더, 파일 잠김 방식
- Bitkeeper: 중앙 통제 방식, 대규모 프로젝트에서 빠른 속도
- Git: 분산 저장소, 커밋/푸시
- Clear Case: 복수 서버/복수 클라이언트, 서버 추가, 확장성

# <mark style='background-color: #fed3fe'>02 공통 모듈 구현</mark>

## <mark style='background-color: #fdb5bd'>1. 공통 모듈 구현</mark>

### <mark style='background-color: #ffdce0'>(2) 공통 모듈 구현의 개념</mark>

2\. 모듈의 독립성을 높이려면 결합도는 약하게, 응집도는 강하게, 모듈의 크기는 작게 해야함

### <mark style='background-color: #ffdce0'>(3) 소프트웨어 모듈 응집도</mark>

<mark style='background-color: #fff5b1'>1. 응집도 개념</mark>

- 모듈의 독립성을 나타내는 정도
- 모듈 내부 구성요소 간 연관 정도

<mark style='background-color: #fff5b1'>2. 응집도 유형</mark>

(나쁨)

1. 우연적(Coincidental): 연관x
2. 논리적(Logical): 유사
3. 시간적(Temporal): 특정 시간
4. 절차적(Procedural): 순차적
5. 통신적(Communication): 동일한 입출력
6. 순차적(Sequential): 출력값 다른 활동이 사용
7. 기능적(Functional): 기능
   (좋음)

### <mark style='background-color: #ffdce0'>(4) 소프트웨어 모듈 결합도</mark>

<mark style='background-color: #fff5b1'>1. 결합도 개념</mark>

- 외부의 모듈과의 연관도, 모듈 간의 상호의존성
- 모듈 간의 관련성을 측정하는 척도

<mark style='background-color: #fff5b1'>2. 결합도 유형</mark>

(나쁨)

1. 내용(Content): 변수, 기능
2. 공통(Common): 전역 변수
3. 외부(External): 외부 모듈
4. 제어(Control): 제어 신호
5. 스탬프(Stamp): 배열, 객체, 구조 전달
6. 자료(Data): 파라미터
   (좋음)

### <mark style='background-color: #ffdce0'>(5) 팬인 팬아웃</mark>

- 팬인: 자신을 기준으로 모듈에 들어오는 것
- 팬아웃: 자신을 기준으로 모듈에서 나가는 것

## <mark style='background-color: #fdb5bd'>2. 공통 모듈 테스트</mark>

### <mark style='background-color: #ffdce0'>(1) 공통 모듈 테스트의 개념</mark>

- 화이트박스 기법
- xUnit : 대표적인 단위 테스트 도구
  - jUnit: 자바
  - CppUnit: C++
  - HttpUnit: 웹 브라우저 없이 웹사이트 테스트
