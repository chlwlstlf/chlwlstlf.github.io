---
layout: single
title: "[우테코]Level1 자동차 경주 게임"
categories: woowacourse
toc: true
toc_sticky: true
---

# 🚗 자동차 경주 게임

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

하지만 다른 곳에서 Car.name 으로 적어두면 이 코드가 getter인지 setter인지 다른 사람들이 이해하기 어렵게 된다. 이렇기 때문에 get 접근자를 생략하여 혼란을 피하고 메서드 호출로 직관적이게 코드를 짜게 된다.

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

<br>
<br>

## <mark class="pink">🔥2단계 구현 사항</mark>

[2단계- 자동차 경주](https://github.com/woowacourse/javascript-racingcar/pull/327){: .btn .btn--primary}

### <mark class="yellow">1. 테스트 코드에서 랜덤 모킹 없애기</mark>

**이전 코드**

forward 안에서 randomNumber를 생성했다.

Car.js

```js
forward() {
  const randomNumber = Random.create();

  if (randomNumber >= FORWARD_CONDITION) this.#location += 1;
}
```

<br>

**공통 피드백**

테스트하기 어려운 부분은 분리하고 테스트 가능한 부분을 단위 테스트한다.  
테스트하기 어려운 부분은 단위 테스트하지 않아도 된다.

<br>

**수정한 코드**

테스트하기 어려운 부분은 `Random.create()` 함수다. 이것을 분리하자.

randomNumber을 인자로 주입받아 forward 함수를 테스트할 때 모킹대신 forward 인자로 내가 지정한 랜덤값을 넣어준다.

Car.js

```js
forward(randomNumber) {
  if (randomNumber >= FORWARD_CONDITION) this.#location += FORWARD;
}
```

---

Car.test.js

```js
describe("각 자동차가 올바르게 전진하고 정지하는 지 테스트", () => {
  test("4일 때는 전진하고 3일 때는 전진하지 않는다.", () => {
    // given
    const FORWARD = 4;
    const STOP = 3;
    const RANDOMS = [FORWARD, FORWARD, STOP, STOP];
    const outputs = [1, 2, 2, 2];

    //when
    const car = new Car("pobi");

    // then
    outputs.forEach((output, i) => {
      car.forward(RANDOMS[i]); // 인자로 넣어주기
      expect(car.getLocation()).toEqual(output);
    });
  });
});
```

<br>
<br>

### <mark class="yellow">2. 재입력 오류 해결하기</mark>

1단계 때 재입력을 성공하지 못 하였는데 그 부분 테스트 코드를 짜지 않아 알지 못 하였다.

각 도메인 안에서 유효성 검사를 하고 있기 때문에 그 부분까지 묶어서 catchReturn 콜백 함수로 넘겨주었다.

```js
async #input() {
  this.#cars = await catchReturn(this.#getCars);
  this.#tryCount = await catchReturn(this.#getCount);
}

async #getCars() {
  const names = await InputView.readCarNames();
  return new Cars(names);
}
```

<br>
<br>

## <mark class="pink">🔥2단계 피드백</mark>

### <mark class="yellow">1. 에러 핸들러 함수 만들기</mark>

**이전 코드**

`catch`문에서 바로 `Console.print(e.message);`를 했다.

```js
import Console from "./Console.js";

const catchReturn = async (callback) => {
  let result;

  while (1) {
    try {
      result = await callback();
      return result;
    } catch (e) {
      Console.print(e.message);
    }
  }
};

export default catchReturn;
```

<br>

**피드백**

에러를 어떻게 처리할 지 계속 바뀔 수 있다. (toast, text, alert 등등)

errorHandler 함수를 만들어 이 안에 어떻게 처리할 지 코드를 짜면 된다.

<br>

**수정한 코드**

catchReturn.js

`catch`문에서 바로 `errorHandler`를 호출하고 `errorHandler`에서 error 처리를 어떻게 할 것인지 작성하였다.

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

---

errorHandler.js

```js
const errorHandler = (err) => {
  console.log(err.message);
};
```

<br>
<br>

### <mark class="yellow">1. 에러 핸들러 함수 만들기</mark>

**이전 코드**

```js
const createRandom = () => {
  return Math.floor(Math.random() * 10);
};
```

<br>

**피드백**

기왕 유틸로 작성된 거 좀 더 유연하면 좋겠다. 0~9라는 범위는 이 함수의 내부 정책인데 재사용성이 높은 유틸이 자기만의 정책을 많이 가지고 있을 필요는 없다. 차라리 범위를 인자로 받는 게 좋을 것 같다.

<br>

**수정한 코드**

utils 파일에서 제공하는 함수들은 일반적으로 여러 모듈에서 재사용 가능한 유틸리티 함수들이다. 이 함수들은 주로 반복적인 작업을 추상화하여 코드의 중복을 줄이고, 재사용성과 확장성을 높이는 역할을 한다. 따라서 내부에서 특정 값으로 함수를 만드는 것이 아닌 변경이 가능한 값들은 외부에서 인자로 주입받게 되는 형식이다.

여기서 min과 max를 주입 받음으로써 `min과 max 사이의 랜덤값을 생성하는 함수`라는 util 함수가 탄생하게 된다.

```js
const createRandom = (min, max) => {
  return Math.floor(Math.random() * (max - min + 1)) + min;
};
```

## <mark class="pink">🔥개념과 함께 채우기</mark>

### <mark class="yellow">1. 함수 선언 방식의 이해</mark>

**함수 선언문과 함수 표현식의 차이점은 무엇일까?**

1\. 함수 선언문

- 함수 선언으로 생성된 함수는 Function 객체
- 함수 호이스팅으로 동작: 아래에서 선언하더라도 Function이 실행 컨텍스트에 들어가서 위에서 호출할 수 있다.

```js
hoisted(); // "foo"

function hoisted() {
  console.log("foo");
}
```

<br>

2\. 함수 표현식

- 화살표 함수로 사용 가능
- 변수 호이스팅처럼 동작: var면 undefined 출력, let이나 const면 참조 에러

```js
console.log(notHoisted); // undefined

notHoisted(); // TypeError: notHoisted is not a function

var notHoisted = function () {
  console.log("bar");
};
```

<br>
<br>

### <mark class="yellow">2. 클래스, 함수 그리고 객체</mark>

**어떤 경우에 class를 사용하고, 어떤 경우에 일반 객체를 사용할까?**

<br>

**class와 함수의 차이는 무엇일까?**

<br>
<br>

### <mark class="yellow">3. 비동기 처리의 다양한 접근</mark>

**비동기 입력을 처리할 때 callback, promise, async/await 중 어떤 것을 활용했고 왜 선택했는지?**

<br>

**비동기 코드의 에러 처리 방법은 어떻게 다를까?**

<br>
<br>

### <mark class="yellow">4. 모듈 시스템의 이해</mark>

**require/module.exports와 import/export 의 차이점은 무엇인가?**

<br>
<br>

### <mark class="yellow">5. 명령형 VS 선언형 프로그래밍</mark>

**명령형 프로그래밍과 선언형 프로그래밍의 핵심적인 차이는 무엇인가? 각각의 접근 방식이 주는 장점과 단점은 무엇인가?**

<br>

**실제 개발 시, 어떤 상황에서 명령형 접근 방식이 유리하고, 언제 선언형 접근 방식을 선택하는 것이 좋을까? 구체적인 예시를 들어 설명해 보자.**
