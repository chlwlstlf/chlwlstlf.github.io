---
layout: single
title: "[우테코]Level1 자동차 경주 게임"
categories: woowacourse
toc: true
toc_sticky: true
---

# 🚗 자동차 경주 게임

## <mark style='background-color: #ffdce0'>1단계 PR 메세지</mark>

**1\. 모델에서 유효성 검사하기**

> 모델에서 각각의 유효성 검사를 하는 `#validate` 메소드를 작성했다.

- `Cars`에서는 문자열로 받은 값을 배열로 바꾼 후 공백과 중복 검사를 한다.
- 이를 통과했다면 배열 각각의 이름은 `Car` 안에서 이름이 몇 글자인지 확인한다.

<br/>

**2\. 도메인에서 도메인으로 값을 내보낼 때: getter**

> `get name()` 에서 `getName()` 으로 변경했다.

- `get name()`은 다른 곳에서 .name으로 값처럼 불러올 수 있는데 이때 name이 name을 불러오는 것인지 할당하는 것인지 다른 사람들이 알기 어렵다.
- 명확한 이름인 `getName()`으로 바꾼 후 함수를 불러오는 것으로 변경했다.

<br>
<br>

## <mark style='background-color: #ffdce0'>1단계 피드백</mark>

**1\. regex 대신 isNan 사용하기**

- regex가 읽기 좋은 도구는 아니다.
- isNan, isNumber 같은 함수를 사용하면 훨씬 직관적인 코드다.

<br/>

**2\. 클래스가 필요할 때만 사용하기**

- 객체를 만드는 방법에는 object, class 등이 있음
- 클래스 안의 메서드가 여러 파일에서 사용되면 static을 붙여 전역으로 쓸 수 있다.
- 클래스의 모든 메서드가 static이라면?

  ```js
  class Console {
    static print(message) {}

    static read(query) {}
  }
  ```

- 클래스가 아니라 객체로 바꾸는 게 맞다.

  ```js
  const Console = {
    print(message) {}

    read(query) {}
  }
  ```

<br>
<br>

## <mark style='background-color: #ffdce0'>1단계 수정 사항</mark>

**1\. 상수화 변경하기**

- ‘:’ 과 ‘,’ 를 상수화 했었는데 상수 이름이 COLON과 COMMA였기 때문에 만약 ‘:’ 이 ‘-’으로 바뀌게 되면 COLON 이라는 이름도 바꿔야 한다고 생각해서 상수화한 것을 없앴다.
- 피드백을 반영하여 1을 FORWARD로 상수화하였다.

<br/>

**2\. 불필요한 class를 변경하기**

- InputView, OutView, console은 안에 static 밖에 없어서 class 역할을 제대로 하지 못 했다. 따라서 객체로 묶어서 쓰는 방식으로 변경했다.
- createRandom도 class였는데 여러 메소드나 인자를 묶어서 사용하지 않고 랜덤한 값을 return하는 기능만 하면 되기 때문에 함수로 변경했다. 또 createRandom이 자동차 경주에 직접적인 영향을 주지 않는다고 생각하여 utils 폴더에 넣어주었다. (꼭 랜덤이 아니어도 forward 조건은 문제에 따라 언제든지 바뀔 수 있다고 생각했다.)

<br/>

**3\. 재입력 오류 해결하기**

- 1단계 때 재입력을 성공하지 못 하였는데 그 부분 테스트 코드를 짜지 않아 알지 못 하였다.
- 각 도메인 안에서 유효성 검사를 하고 있기 때문에 그 부분까지 묶어서 catchReturn 콜백 함수로 넘겨주었다.

  ```js
  async #input() {
    this.#cars = await catchReturn(this.#getCars);
    this.#tryCount = await catchReturn(this.#getCount);
  }
  ```

<br>
<br>

## <mark style='background-color: #ffdce0'>2단계 피드백</mark>

**1\. 에러 핸들러 함수 만들기**

- 에러를 어떻게 처리할 지 계속 바뀔 수 있다. (toast, text, alert 등등)
- errorHandler 함수를 만들어 이 안에 어떻게 처리할 지 코드를 짜면 된다.
  ```js
  const errorHandler = (err) => {
    console.log(err.message);
  };
  ```

**2\. 상수화 네이밍 변경하기**

- COMMA나 COLON 대신 DELIMITER나 SEPARATOR를 사용한다.
