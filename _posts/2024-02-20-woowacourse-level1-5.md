---
layout: single
title: "[우테코]Level1 TDD, 객체"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션2 LMS 강의 자료1

## <mark class="pink">📖 TDD와 리팩터링</mark>

**1\. Test Driven Development(테스트 주도 개발, TDD)**

- 테스트를 먼저 작성하고 그 후 실제 코드를 작성하는 방법
- 문제를 작은 단위로 정의하고, 피드백을 자주 받으면서 그 해답을 찾아가는 과정

<br/>

**2\. TDD 사이클**

- 빨강 - 실패하는 작은 테스트
- 초록 - 빨리 테스트가 통과하게끔 만든다.
- 파랑 - 리팩토링: 모든 중복을 없앤다.

<br/>

**3\. 고려할 점**

- 생성자를 추가하는 것을 추천
- 메소드 추가는 비추천

<br/>
<br/>

## <mark class="pink">📖 프로그래밍 패러다임 종류</mark>

**1\. 명령형 프로그래밍**

- 문제를 어떻게(HOW) 해결할 것인지에 초점
- 명령어들로 구성
- 절차적, 객체지향 프로그래밍

<br/>

**1-1. 절차적 프로그래밍**

- 명령어의 목록

<br/>

**1-2. 객체 지향 프로그래밍**

- 여러 개의 독립적 단위, 객체의 모임
- 클래스로 발전
- 추상화, 상속, 다형성, 캡슐화
- 다형성: 메서드의 인자의 개수나 자료형에 따라서 다른 기능
- 캡슐화: 객체의 세부 구현 내용을 숨긴다.(보안, 유지보수, 재사용)
- 함수의 비일관성
- 객체 내 상태 변화

<br/>

**2\. 선언형 프로그래밍**

- 무엇(WHAT)에 초점
- 함수형, 논리형 프로그래밍

<br/>

**2-1. 함수형 프로그래밍**

- 함수의 정의들로 구성
- 함수들의 집합
- 개발자가 더 중요한 문제에 집중할 수 있도록
- 일급 함수: 변수에 함수 할당, 함수 인자 전달, 함수 반환
  ```jsx
  const numbers = [1, 2, 3];
  const result = numbers.map(function (number, index) {
    return number * 2;
  }); //[2, 4, 6]
  ```
- 순수 함수: 동일한 인자가 들어오면 항상 동일한 결과를 반환
- 불변성: 최초 생성된 값이 변경되지 않음
  ```jsx
  const newArray = [...originals, 4];
  ```

<br/>
<br/>

## <mark class="pink">📖 객체</mark>

**1\. 모듈화**

- 협력젹: 외부의 도움을 받는 객체, 스스로 처리x
- 자율적: 내부와 외부를 명확하게 구분, 다른 객체가 '무엇'을 수행하는 지는 알 수 있지만 '어떻게' 수행하는 지는 모른다.
- 단일 책임: 한 가지 기능을 변경할 때에는 하나의 클래스만 변경
- 캡슐화: 객체의 데이터를 직접 꺼대지 않고, 객체에 메세지를 던지도록 구조를 바꾼다.

<br/>
<br/>

## <mark class="pink">📖 유효성 검사 중복될 때</mark>

> 상속보다는 조합을 사용하자

- 조합 (먼저 생각하기)

  ```jsx
  class WinningLotto {
    constructor(winningLotto, bonusNumber) {
      if (winningLotto.has(bonusNumber)) {
        throw new Error("보너스 번호는 당첨 번호와 중복될 수 없습니다.");
      }

      this.winningLotto = winningLotto;
      this.bonusNumber = bonusNumber;
    }
  }

  new WinningLotto(new Lotto([1, 2, 3, 4, 5, 6]), 7);
  ```

- 상속

  ```jsx
  class WinningLotto extends Lotto {
    constructor(numbers, bonusNumber) {
      super(numbers);

      if (numbers.includes(bonusNumber)) {
        throw new Error("보너스 번호는 당첨 번호와 중복될 수 없습니다.");
      }

      this.bonusNumber = bonusNumber;
    }
  }

  new WinningLotto([1, 2, 3, 4, 5, 6], 7);
  ```
