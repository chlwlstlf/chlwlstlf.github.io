---
layout: single
title: "[우테코]Level1 TDD, 객체"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션2 LMS 강의 자료1

## <mark class="pink">📖TDD와 리팩터링</mark>

**<mark class="yellow">Test Driven Development(테스트 주도 개발, TDD)</mark>**

- 테스트를 먼저 작성하고 그 후 실제 코드를 작성하는 방법
- 개발 전에 테스트 코드를 짜는 것보단 **문제를 작은 단위로 정의하고, 피드백을 자주 받으면서 그 해답을 찾아가는 과정**이 TDD의 기본 취지

<br>

**<mark class="yellow">TDD 사이클</mark>**

![](https://techcourse-storage.s3.ap-northeast-2.amazonaws.com/6330f66bc3ba4670b61364b8ece706b9)

- 빨강 - 실패하는 작은 테스트
- 초록 - 빨리 테스트가 통과하게끔 만든다.
- 파랑 - 리팩토링: 중복 코드 제거, 일반화

<br>

**<mark class="yellow">고려할 점</mark>**

- 생성자를 추가하는 것을 추천
- 메소드 추가는 비추천

<br>
<br>

## <mark class="pink">📖프로그래밍 패러다임 종류</mark>

### <mark class="yellow">명령형 프로그래밍</mark>

- 문제를 어떻게(HOW) 해결할 것인지에 초점
- 명령어들로 구성
- 절차적, 객체지향 프로그래밍

<br>

**1\. 절차적 프로그래밍**

- 명령어의 목록

<br>

**2\. 객체 지향 프로그래밍**

- 여러 개의 독립적 단위, 객체의 모임
- 클래스로 발전
- 추상화, 상속, 다형성, 캡슐화
- 다형성: 메서드의 인자의 개수나 자료형에 따라서 다른 기능
- 캡슐화: 객체의 세부 구현 내용을 숨긴다.(보안, 유지보수, 재사용)
- 함수의 비일관성
- 객체 내 상태 변화

<br>

### <mark class="yellow">선언형 프로그래밍</mark>

- 무엇(WHAT)에 초점
- 함수형, 논리형 프로그래밍

<br>

**1\. 함수형 프로그래밍**

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

<br>
<br>

## <mark class="pink">📖객체</mark>

**<mark class="yellow">모듈화</mark>**

- 협력적: 외부의 도움을 받는 객체, 모든 것을 스스로 처리하는 객체는 복잡해진다.
- 자율적: 내부와 외부를 명확하게 구분, 다른 객체가 '무엇'을 수행하는 지는 알 수 있지만 '어떻게' 수행하는 지는 모른다.
- 단일 책임: 한 가지 기능을 변경할 때에는 하나의 클래스만 변경
- 캡슐화: 객체의 데이터를 직접 꺼내지 않고, 객체에 메세지를 던지도록 구조를 바꾼다.

<br>
<br>

## <mark class="pink">📖유효성 검사 중복될 때</mark>

> 상속보다는 조합을 사용하자

**<mark class="yellow">조합 (먼저 생각하기)</mark>**

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

<br>

**<mark class="yellow">상속</mark>**

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

<br>
<br>

## <mark class="pink">📖함수</mark>

**JavaScript는**

- **멀티 패러다임** 프로그래밍 언어: 명령형, 함수형, 프로토타입 기반 객체지향 프로그래밍을 지원
- 일반적인 호스트 환경은 브라우저이지만, 서버 사이드, 데스크탑 앱 등 점차 다양한 호스트 환경에서 사용되고 있다.
- 함수 역시 하나의 객체

<br>

### <mark class="yellow">일급 함수</mark>

JS에서 함수는 일급 객체

**고차 함수**

- 다른 함수를 인자로 받거나, 함수를 리턴하는 함수를 만들 수 있다.
- callback, 다양한 배열 내장 메서드

<br>

**클로저**

- 함수의 private state가 필요하다면 클로저를 활용한다.
- 아래는 클로저 예시이다. 이 경우 클로저를 사용할 필요는 없지만 예시의 하나로 보면 좋을 것 같다.

```js
const createRandomGenerator = () => {
  const lottoNumbers = new Array(45).fill().map((_, index) => index + 1);

  return () => {
    lottoNumbers.sort(() => Math.random() - 0.5);
    return lottoNumbers.slice(0, 6);
  };
};
```

```js
const generateLottoNumbers = createRandomGenerator();
console.log(generateLottoNumbers());
// 예시: [7, 14, 19, 23, 35, 42]
```

<br>

**화살표 함수**

- 함수 본문이 간단한 return문만 포함할 때는 화살표 함수 문법을 사용하면 간결하게 작성할 수 있다.
- 함수가 정의된 스코프의 this를 사용하기 때문에 함수를 다른 곳으로 전달할 때의 this에 대한 혼란을 줄일 수 있다.
- 단일 표현식을 반환할 때 중괄호 `{}`를 생략하면 `return`이 필요 없다.

```js
function shuffle(array) {
  return array.sort(() => Math.random() - 0.5);
}
// vs
const shuffle = (array) => array.sort(() => Math.random() - 0.5);
```

<br>

### <mark class="yellow">함수형 프로그래밍</mark>

**1\. 가능하면 순수 함수로 작성한다.**

- 함수의 파라미터가 함수의 input을 제공하고, 주어진 이 input에 의해서 output이 결정되도록 만든다.
- 이러한 함수를 부수 효과가 없는 순수 함수라고 부른다.

```js
// not pure
const numbers = [1, 2, 3, 4, 5];
function shuffle() {
  return numbers.sort(() => Math.random() - 0.5);
}

// pure
function shuffle(numbers) {
  return numbers.sort(() => Math.random() - 0.5);
}
```

<br>

하지만 순수 함수로만 어플리케이션을 만들 수는 없다. 아래는 중요한 부수 효과들이다.

<div class="blue-box">
  <p>
    <div>브라우저에서의 이벤트 처리</div>
    <div>화면에 출력</div>
    <div>API 통신</div>
    <div>웹 API를 사용하는 코드</div>
    <div>데이터베이스 읽기/쓰기</div>
  </p>
</div>

<br>

- 순수 함수는 동작을 예측하기 쉽다. 주어진 input이 같다면 항상 같은 output을 기대할 수 있기 때문이다.
- 순수 함수는 테스트하기 쉽다. 함수 외부의 요소에 의존하는 것이 없으며, input과 output이 명확하다.
- 테스트하기 어려운 랜덤과 같은 요소들을 분리해냈듯이, 부수 효과가 있는 함수들은 가능한 부수 효과를 위해서만 동작할 수 있도록 따로 구분한다.

<br>

**2\. 데이터를 불변으로 유지한다.**

변수를 선언할 때는 const를 활용한다.

- let 대신 const를 사용하는 것을 지향한다.
- 함수를 분리하거나 선언형 방식으로 리팩토링하게 될 것이다.
- 참조형(객체, 배열)으로 되어있는 자료형은 const일 때 참조 값 자체를 재할당할 수는 없지만 내부 속성이나 값을 변경할 수 있다.
- Object.freeze()로 이를 해결할 수 있다.

<br>

값을 변경할 때에는 복사본을 만들어 활용한다.

```js
const numbers = [1, 3, 5, 4, 2];

const sortNumbers = (numbers) => {
  return numbers.sort((a, b) => a - b);
};

console.log(numbers); // 원본 배열 바뀜 [1, 2, 3, 4, 5]
```

```js
const numbers = [1, 3, 5, 4, 2];

const sortNumbers = (numbers) => {
  return [...numbers].sort((a, b) => a - b);
};

console.log(numbers); // 원본 배열 안 바뀜 [1, 3, 5, 4 ,2]
```

<br>

**복사본 만들기**

- slice()

  ```js
  const copied = numbers.slice();
  ```

- Object.assign()

  ```js
  const copied = Object.assign({}, object);
  ```

- 전개 연산자(spread operator)

  ```js
  const copied = [...numbers];
  ```

<br>

**3\. 선언형 코드를 지향한다.**

- 함수로 분리하고 함수의 이름으로 어떤 동작을 하는지를 드러낸다.
- 함수를 인자로 넘겨줄 때도 필요하다면 기명함수로 만들어 가독성을 높인다.
