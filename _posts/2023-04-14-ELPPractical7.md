---
layout: archive
title: "정보처리기사 7단원"
categories: elp
toc: true
toc_sticky: true
---

# VII SQL 응용

---

# <mark style='background-color: #fed3fe'>01 데이터베이스 기본</mark>

## <mark style='background-color: #fdb5bd'>1. 트랜잭션</mark>

### <mark style='background-color: #ffdce0'>(1) 트랜잭션</mark>

2\. 특성

- 원자성: 전체 성공 또는 실패
- 일관성: 트랜잭션 수행 전과 후 같음
- 격리성: 서로 영향x
- 영속성: 영속적으로 저장

5\. 병행 제어 기법 종류

- 로킹: 상호배제, 로킹 단위↓ → 데베 공유도↑, 오버헤드↑
- 낙관적 검증: 트랜잭션 종료 시 검증
- 타임 스탬프 순서: 실행 전에 타임 스탬프 부여
- 다중버전 동시성 제어: 직렬가능성 보장 되는 버전 선택

7\. 회복 기법
주요 요소

- REDO: 시작, 완료 기록으로 재작업
- UNDO: 시작은 있지만, 완료 기록 없는 트랜잭션 취소

회복 기법 종류

- 로그 기반 회복 기법: 지연 갱신 회복 기법/ 즉각 갱신 회복 기법
- 체크 포인트 회복 기법: 검사점 이후에 처리된 트랜잭션만 복원
- 그림자 페이징 회복 기법: 복제본 생성

### <mark style='background-color: #ffdce0'>(2) DDL</mark>

1\. 데이터 정의어: 구조 생성, 변경, 삭제

2\. DDL 대상

- 도메인
- 스키마: 외부/개념/내부 스키마
- 테이블
- 뷰
- 인덱스
  - 순서 인덱스: 정렬
  - 해시 인덱스: 해시 함수
  - 비트맵 인덱스: 적은 개수 값
  - 함수기반 인덱스: 수식, 함수
  - 단일 인덱스: 하나 컬럼
  - 결합 인덱스: 두 개 이상 컬럼
  - 클러스터드 인덱스: 기본 키 기준으로 레코드 묶어서

3\. DDL 명령어

- 생성: CREATE
- 수정: ALTER
- 삭제: DROP, TRUNCATE

4\. TABLE DDL

- CREATE (테이블 생성)

```sql
CREATE TABLE 테이블명
{
  컬럼명 데이터타입 PRIMARY KEY,
  컬럼명 데이터타입 FOREIGN KEY REFERENCES,
  컬럼명 데이터타입 UNIQUE,
  컬럼명 데이터타입 NOT NULL,
  컬럼명 데이터타입 CHECK(조건식),
  컬럼명 데이터타입 DEFAULT,
};
```

- ALTER (테이블 수정)

```sql
ALTER TABLE 테이블명 ADD 컬럼명 데이터타입 제약조건; --추가
ALTER TABLE 테이블명 MODIFY 컬럼명 데이터타입 제약조건; --수정
ALTER TABLE 테이블명 DROP 컬럼명 데이터타입 제약조건; --삭제
```

- DROP (테이블 삭제)

```sql
DROP TABLE 테이블명 CASCADE | RESTRICT;
```

- TRUNCATE (테이블 내 데이터 삭제)

```sql
TRUNCATE TABLE 테이블명;
```

5\. VIEW DDL

- CREATE VIEW (뷰 생성)

```sql
CREATE VIEW 뷰이름 AS
조회쿼리;
```

- CREATE OR REPLACE VIEW (뷰 교체)

```sql
CREATE OR REPLACE VIEW 뷰이름 AS
조회쿼리;
```

- DROP VIEW (뷰 삭제)

```sql
DROP VIEW 뷰이름;
```

6\. INDEX DDL

- CREATE INDEX (인덱스 생성)

```sql
CREATE INDEX 인덱스명 ON 테이블(컬럼명1, 컬럼명2, ...);
```

- ALTER INDEX (인덱스 수정)

```sql
ALTER INDEX 인덱스명 ON 테이블(컬럼명1, 컬럼명2, ...);
```

- DROP INDEX (인덱스 삭제)

```sql
DROP INDEX 인덱스명;
```

### <mark style='background-color: #ffdce0'>(3) DML</mark>

2\. DML 명령어

- 조회: SELECT

- 삽입: INSERT

  ```sql
  INSERT INTO 테이블명(속성명1, ...)
  VALUES (데이터1, ...);
  ```

- 갱신: UPDATE

  ```sql
  UPDATE 테이블명 SET 속성명=데이터, ...
  WHERE 조건;
  ```

- 삭제: DELETE
  ```sql
  DELETE FROM 테이블명
  WHERE 조건;
  ```

### <mark style='background-color: #ffdce0'>(4) DCL</mark>

1\. DCL 명령어

- GRANT (권한 부여)

  ```sql
  GRANT 권한 ON 테이블 TO 사용자;
  ```

- REVOKE (권한 회수)
  ```sql
  REVOKE 권한 ON 테이블 FROM 사용자;
  ```
