---
layout: single
title: "[우테코]Level1 자동차 경주 게임 1단계"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션1 🚗자동차 경주 게임 1단계

## <mark class="pink">🔥1단계 구현 사항</mark>

[1단계- 자동차 경주](https://github.com/woowacourse/javascript-racingcar/pull/254){: .btn .btn--primary}

### <mark class="yellow">주요 코드</mark>

**Cars.js**

```js
import ERROR_MESSAGE from "../error/message.js";
import { COMMA } from "../view/OutputView.js";
import Car from "./Car.js";

class Cars {
  #cars;

  constructor(names) {
    const parsedNames = this.#parse(names);

    this.#validate(parsedNames);

    this.#cars = parsedNames.map((name) => new Car(name));
  }

  #parse(names) {
    return names.split(COMMA).map((name) => name.trim());
  }

  #validate(names) {
    const uniqueNames = new Set(names);

    if (uniqueNames.size !== names.length)
      throw new Error(`${ERROR_MESSAGE.duplicated} ${ERROR_MESSAGE.retry}`);
  }

  play() {
    this.#cars.forEach((car) => {
      car.forward();
    });

    return this.#cars.map((car) => ({
      name: car.getName(),
      location: car.getLocation(),
    }));
  }

  winners() {
    const maxLocation = Math.max(...this.#cars.map((car) => car.getLocation()));
    return this.#cars
      .filter((car) => car.getLocation() === maxLocation)
      .map((car) => car.getName());
  }
}

export default Cars;
```

<br>

**Car.js**

```js
import ERROR_MESSAGE from "../error/message.js";
import Random from "./Random.js";

export const MIN_NAME_LENGTH = 1;
export const MAX_NAME_LENGTH = 5;
const FORWARD_CONDITION = 4;

class Car {
  #name;
  #location = 0;

  constructor(name) {
    this.#validate(name);

    this.#name = name;
  }

  #validate(name) {
    if (name.length < MIN_NAME_LENGTH || name.length > MAX_NAME_LENGTH) {
      throw new Error(`${ERROR_MESSAGE.nameLength} ${ERROR_MESSAGE.retry}`);
    }
  }

  forward() {
    const randomNumber = Random.create();

    if (randomNumber >= FORWARD_CONDITION) this.#location += 1;
  }

  getName() {
    return this.#name;
  }

  getLocation() {
    return this.#location;
  }
}

export default Car;
```

<br>

**TryCount.js**

```js
import ERROR_MESSAGE from "../error/message.js";

export const MIN_TRY_COUNT = 1;
export const MAX_TRY_COUNT = 10;

class TryCount {
  #tryCount;

  constructor(tryCount) {
    this.#validate(tryCount);

    this.#tryCount = Number(tryCount);
  }

  #validate(tryCount) {
    const numberRegExp = /^[0-9]+$/;

    if (tryCount === "")
      throw new Error(`${ERROR_MESSAGE.tryCountEmpty} ${ERROR_MESSAGE.retry}`);
    if (!numberRegExp.test(tryCount))
      throw new Error(`${ERROR_MESSAGE.isNan} ${ERROR_MESSAGE.retry}`);
    if (Number(tryCount) < MIN_TRY_COUNT || Number(tryCount) > MAX_TRY_COUNT)
      throw new Error(`${ERROR_MESSAGE.tryCountRange} ${ERROR_MESSAGE.retry}`);
  }

  getTryCount() {
    return this.#tryCount;
  }
}

export default TryCount;
```

<br>
<br>

### <mark class="yellow">1. 모델에서 유효성 검사하기</mark>

**구현 사항**

모델에서 각각의 유효성 검사를 하는 `#validate` 메소드를 작성했다.

`Cars`에서는 문자열로 받은 값을 배열로 바꾼 후 공백과 중복 검사를 한다.

이를 통과했다면 배열 각각의 이름은 `Car` 안에서 이름이 몇 글자인지 확인한다.

<br>

**이유**

유효성 검사를 util 함수에서 모든 유효성 검사를 하는 것도 좋은 추상화지만 한 클래스가 모든 기능을 담당하길 원했다.  
따라서 한 클래스에서 파싱과 유효성 검사를 모두 했고, 이를 통과하면 접근 제어자(#)에 인자로 받은 값을 넣었다.

추가적으로 파싱과 유효성 검사를 메소드를 분리하였다. parse는 레이서 이름을 ','로 나누는 데에만 집중하고, validate는 문자열이 올바른지 확인하는 데만 집중하였다. 한 가지 일만 담당하기에 디버깅이 쉽고 테스트 코드를 짜기도 편해졌다.

<br>
<br>

### <mark class="yellow">2. 도메인에서 도메인으로 private 값을 내보낼 때: getter</mark>

**구현 사항**

`get name()` 에서 `getName()` 으로 변경했다.

Car.js

```js
getName() {
  return this.#name;
}

getLocation() {
  return this.#location;
}
```

<br>

**이유**

[Airbnb JavaScript Style Guide - Accessors](https://github.com/airbnb/javascript?tab=readme-ov-file#accessors)를 따랐다.

get 접근자는 name과 같이 속성의 이름을 그대로 사용하도록 권장된다. 보통 접근 제어자에 읽기로 접근할 때 사용하기 때문에 아래처럼 코드를 짠다. 이렇게 되면 외부에서 메서드가 아니라 속성에 접근하듯이 사용할 수 있다.

```js
get name() {
  return this.#name;
}
```

하지만 다른 곳에서 `Car.name` 으로 적어두면 이 코드가 getter인지 setter인지 다른 사람들이 이해하기 어렵게 된다. 이렇기 때문에 get 접근자를 생략하여 혼란을 피하고 메서드 호출로 직관적이게 코드를 짜게 된다.

```js
getName() {
  return this.#name;
}
```

<br>
<br>

### <mark class="yellow">3. 반복 입력일 때 while? 재귀?</mark>

**구현 사항**

반복으로 입력 받을 때 재귀대신 while문을 사용하였다.

catchReturn.js

```js
import errorHandler from "./errorHandler";

const catchReturn = async (callback) => {
  while (true) {
    try {
      const result = await callback();
      return result;
    } catch (err) {
      errorHandler(err);
    }
  }
};
```

<br>

**이유**

계속 입력 받는 방식에는 while문과 재귀 방식이 있다. 이렇게 제한은 없고 조건만 있을 때는 JS에서 재귀에 대한 사용을 지양한다.

재귀적인 에러 처리는 스택 오버플로우의 위험이 있다. 사용자가 계속해서 잘못된 입력을 하면 함수가 계속해서 자기 자신을 호출하는데 이때 각 재귀 호출마다 새로운 스택 프레임이 쌓이게 된다. 여기서 메모리 사용량이 계속 증가하면서 스택 오버플로우가 발생할 수 있다.

또 JavaScript의 경우 대략 10,000번 정도로 재귀 호출 제한이 걸려 있어 while 루프를 사용하는 방식이 좀 더 안전하다.

<br>
<br>

## <mark class="pink">🔥1단계 피드백</mark>

### <mark class="yellow">1. regex 대신 isNan 사용하기</mark>

**이전 코드**

TryCount.js

```js
const numberRegExp = /^[0-9]+$/;

if (!numberRegExp.test(tryCount))
  throw new Error(`${ERROR_MESSAGE.isNan} ${ERROR_MESSAGE.retry}`);
```

<br>

**피드백**

regex가 읽기 좋은 도구는 아니다.

isNan, isNumber 같은 함수를 사용하면 훨씬 직관적인 코드다.

<br>

**수정한 코드**

TryCount.js

```js
if (!Number.isInteger(Number(tryCount))) {
  throw new Error(`${ERROR_MESSAGE.isNan} ${ERROR_MESSAGE.retry}`);
}
```

<br>
<br>

### <mark class="yellow">2. 클래스가 필요할 때만 사용하기</mark>

**이전 코드**

Console.js

```js
class Console {
  static print(message) {}

  static read(query) {}
}
```

<br>

**피드백**

객체를 만드는 방법에는 object, class 등이 있다.

클래스 안의 메서드가 여러 파일에서 사용되면 static을 붙여 전역으로 쓸 수 있다.

클래스의 모든 메서드가 static이라면 클래스로 쓸 필요가 있을까?

<br>

**수정한 코드**

class와 단순 객체

JavaScript에서 객체를 생성하는 주요 방법은 클래스와 단순 객체로 크게 두 가지이다.  
클래스 안에 static 속성이나 메서드만 있는 경우, 해당 클래스는 인스턴스화(new)하지 않고도 메서드를 호출할 수 있다.

클래스를 생성하는 기준을 아래처럼 세 가지로 정해두었다.

1. 상태를 관리해야 할 때
2. 여러 메서드가 공유하는 private 데이터가 필요할 때
3. 인스턴스별로 다른 설정이 필요할 때

---

예를 들어 Cars.js는 클래스가 적합하다고 생각하는데 그 이유는

1. `#cars`라는 상태를 관리하고
2. 여러 메서드가 #cars라는 private 데이터를 공유하고
3. 각 인스턴스마다 다른 로또 번호를 가져야 하기 때문이다.

따라서 Console은 단순 함수들의 집합이기 때문에 단순 객체로 묶거나 개별로 export 하는 것이 좋다고 생각한다.

---

Console.js

```js
const Console = {
  print(message) {}

  read(query) {}
}
```
