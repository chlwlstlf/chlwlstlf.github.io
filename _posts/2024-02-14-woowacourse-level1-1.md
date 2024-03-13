---
layout: single
title: "[우테코]Level1 단위 테스트"
categories: woowacourse
toc: true
toc_sticky: true
---

# 2024년 02월 14일 수업 내용

## <mark style='background-color: #ffdce0'>🧨좋은 코드</mark>

1\. 가독성이 좋은 코드  
다른 사람이 코드를 빠르게 이해할 수 있게 한다.

2\. 예측 가능한 코드  
예외나 에러를 예측하기 어렵고 대처하기도 어려울 수 있다.

3\. 잘못 사용하지 않는 코드  
의도와 다르게 이해하지 않게 할 수 있게 한다.

4\. 모듈화되어 있는 코드  
하나를 변경해도 다른 모듈들에 영향이 가지 않는다.

5\. 재사용하기 쉬운 코드  
한 번 작성한 코드로 다른 유사한 문제들을 해결하는 데에도 사용할 수 있다.

6\. 테스트하기 쉬운 코드  
정상적으로 동작하는 것을 보장하기 위해 테스트 코드를 작성한다.

---

## <mark style='background-color: #ffdce0'>📏단위 테스트</mark>

**기본 용어**

- 프로덕션 코드: 실제 코드, 테스트 대상이 되는 코드
- 테스트 코드: Car.js의 테스트 코드는 Car.test.js

**테스트 케이스**

- given(arrange): 테스트할 동작을 호출하기 위해 필요한 사전 준비 작업
- when(act): 테스트 대상 동작 호출
- then(assertion): 호출 결과가 기대한 결과와 동일한 지 확인

```js
test("랜덤값이 4이상이면 1칸 전진", () => {
  // given
  const car = new Car("우택호");

  // when
  car.move(4);

  // then
  expect(car.position).toBe(1);
});
```

**Setup & Teardown**

- beforeEach, afterEach: 반복적으로 수행해야 하는 작업
- beforeAll, afterAll: 일회성으로 설정
- describe: 이 블록 내부에만 before* 과 after* 를 적용할 수도 있다.

**Jest Matchers**
정의: 다양한 방식으로 값을 테스트할 수 있게 하는 것

종류

- toBe, toBeNull, toBeUndefined, ...
- toEqual(객체 값 같은지 판별할 때)
- toMatch
- toContain
- toThrow

## <mark style='background-color: #ffdce0'>📏단위 테스트 예시</mark>

Calculator.js (프로덕션 코드)

```js
class Calculator {
  add(a, b) {
    return a + b;
  }
  subtract(a, b) {
    return a - b;
  }
  multiply(a, b) {
    return a * b;
  }
  divide(a, b) {
    return a / b;
    if (b === 0) {
      throw new Error("0으로 나눌 수 없음");
    }
  }
}

export default Calculator;
```

Calculator.test.js - AAA 패턴 (테스트 코드)

```js
import Calculator from "../src/Calculator";

describe("계산기 기능 검증", () => {
  //덧셈
  test("두 개의 수를 받아서 덧셈을 한다.", () => {
    // Arrange
    const calculator = new Calculator();
    const a = 1;
    const b = 2;

    // Act
    const result = calculator.add(a, b);

    // Assert
    expect(result).toBe(3);
  });

  //뺄셈
  //위와 비슷

  //곱셈
  //위와 비슷

  //나눗셈
  test("두 개의 수를 받아서 나눗셈을 한다.", () => {
    // Arrange
    const calculator = new Calculator();
    const a = 3;
    const b = 1; //여기가 1이면 검증하기 힘듦

    // Act
    const result = calculator.divide(a, b);

    // Assert
    expect(result).toBe(3);
  });

  test("0으로 나누면 오류를 발생시킨다.", () => {
    // Arrange
    const calculator = new Calculator();

    expect(() => {
      // Act
      calculator.divide(3, 0);
    })
      // Assert
      .toThrow();
  });
});
```

## <mark style='background-color: #ffdce0'>📏단위 테스트 사용 Tip</mark>

💡 팁1. Jest > docs > getting start 에서 도움받을 수 있음
💡 팁2. test.skip(), xtest()는 걔만 빼고 테스트, test.only()는 걔만 테스트

⚠️ 주의. 프로덕션 코드나 테스트 코드 둘 중 하나씩만 리팩토링 하기
