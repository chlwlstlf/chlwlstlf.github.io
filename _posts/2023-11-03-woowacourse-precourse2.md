---
layout: single
title: "프리코스 2주차"
categories: 우아한테크코스
toc: true
toc_sticky: true
---

# 🚗 자동차 경주 게임

<mark style='color: red'>※ 진블로그는 링크가 있는 사람만 조회할 수 있어서 저만의 기록 공간으로 활용하고 있습니다.</mark>

## <mark style='background-color: #ffdce0'>2023.10.26(목)</mark>

### 🧐 1주차 공통 피드백

- 요구 사항, 프로그래밍 요구 사항, 과제 진행 요구 사항의 항목을 모두 잘 지켰는지 다시 한 번 점검한다.
- 커밋 메시지에 해당 커밋에서 작업한 내용에 대한 이해가 가능하도록 작성한다.
- git에 코드를 추가할 때는 git을 통해 관리할 필요가 있는지를 고려해볼 것을 추천한다. (**node modules** 는 **package.json** 파일이 있으면 설치할 수 있고 버전 관리를 직접 하지 않으므로 git으로 관리하지 않아도 된다.)
- 기능 구현을 위해 새로 만든 브랜치에서 작업한 후 PR을 보낸다.
- 미션 제출 기간 이후에는 추가 커밋을 하지 않는다.
- 변수 이름, 함수(메서드) 이름, 클래스 이름을 짓는데 시간을 투자하라. (연속된 숫자를 덧붙이거나(a1, a2, ..., aN), 불용어(Info, Data, a, an, the)를 추가하는 방식은 적절하지 못하다.)
- 의도를 드러낼 수 있다면 이름이 길어져도 괜찮다. (클래스 이름이 Order라면 shipOrder라고 메서드 이름을 지을 필요가 없다. 짧게 ship()이라고 하면 클라이언트에서는 order.ship()라고 호출하며, 간결한 호출의 표현이 된다.)
- if, for, while문 사이의 공백도 코딩 컨벤션이다.
- 가능하면 eslint와 prettier를 이용해 더욱 생산적으로 코드를 작성하자.
- 최종 제출하는 코드에서 EOL을 확인한다.
- JavaScript에서 제공하는 API를 적극 활용한다 (map, join 등)

---

### 🚀 기능 요구사항

<mark style='background-color: #fff5b1'>초간단 자동차 경주 게임을 구현한다.</mark>

- 주어진 횟수 동안 n대의 자동차는 전진 또는 멈출 수 있다.
- 각 자동차에 이름을 부여할 수 있다. 전진하는 자동차를 출력할 때 자동차 이름을 같이 출력한다.
- 자동차 이름은 쉼표(,)를 기준으로 구분하며 이름은 5자 이하만 가능하다.
- 사용자는 몇 번의 이동을 할 것인지를 입력할 수 있어야 한다.
- 전진하는 조건은 0에서 9 사이에서 무작위 값을 구한 후 무작위 값이 4 이상일 경우이다.
- 자동차 경주 게임을 완료한 후 누가 우승했는지를 알려준다. 우승자는 한 명 이상일 수 있다.
- 우승자가 여러 명일 경우 쉼표(,)를 이용하여 구분한다.
  ```jsx
  const members = ["east", "west", "south"];
  members.map((member) => member).join(","); // "east,west,south"
  ```
- 사용자가 잘못된 값을 입력한 경우 throw문을 사용해 "[ERROR]"로 시작하는 메시지를 가지는 예외를 발생시킨 후, 애플리케이션은 종료되어야 한다.

**1\. 시작 입출력**

`경주할 자동차 이름을 입력하세요.(이름은 쉼표(,) 기준으로 구분)`  
`pobi,woni,jun`  
`시도할 횟수는 몇 회인가요?`  
`5`

**2\. 출력**

- 각 차수별 실행 결과
  ```bash
  pobi : --
  woni : ----
  jun : ---
  ```
- 우승자 안내 문구  
  `최종 우승자 : pobi, jun`

---

### 🎯 프로그래밍 요구사항

**※ 추가된 요구 사항**

- indent(인덴트, 들여쓰기) depth를 3이 넘지 않도록 구현한다. 2까지만 허용한다.
  - 예를 들어 while문 안에 if문이 있으면 들여쓰기는 2이다.
  - 힌트: indent(인덴트, 들여쓰기) depth를 줄이는 좋은 방법은 함수(또는 메소드)를 분리하면 된다.
- Jest를 이용하여 본인이 정리한 기능 목록이 정상 동작함을 테스트 코드로 확인한다.
  - 테스트 도구 사용법이 익숙하지 않다면 `__tests__/StringTest.js`를 참고하여 학습한 후 테스트를 구현한다.

## <mark style='background-color: #ffdce0'>2023.10.27(금)</mark>

### 💻 개발 시작

**1차 기능 명세서 작성**

```markdown
🎯 기능 명세서
1\. 경주할 자동차 이름과 횟수 입력받기

- 이름은 5자 이하만 가능하다.(예외처리 필요)
- 횟수는 숫자만 입력해야 한다.(예외처리 필요)
- (추후 확인) 띄어쓰기는 어떻게 처리해야 하는가?
- (추후 확인) 횟수는 제한이 없는가?

2\. 전진할지 정하기

- 0에서 9사이의 무작위 값을 구한다.
- 0, 0, 4 라는 숫자는 상수로 관리한다.

3\. 우승자 출력하기

- 여러 명일 경우 쉼표를 이용하여 구분한다.
- (추후 확인)', '과 ',' 중 어떤 것을 써야하는가?

※ 추가 확인 사항

- indent(인덴트, 들여쓰기) depth를 3이 넘지 않도록 구현한다.
- Jest를 이용하여 본인이 정리한 기능 목록이 정상 동작함을 테스트 코드로 확인한다.

※ 만들어야할 것

- 상수, 메세지 관리 파일
- 숫자 랜덤 추출 클래스
```

**(_default)_ index.js**

```jsx
import App from "./App.js";

const app = new App();
app.play();
```

---

## <mark style='background-color: #ffdce0'>2023.10.28(토)</mark>

### 💻 개발

**폴더 구조**

```
src/
├── components/
│   ├── CreateNumber.js
│   ├── RacingCar.js
│   └── ValidateInput.js
├── constants/
│   ├── AppConstants.js
│   └── Messages.js
├── App.js
└── index.js
```

---

**constants 폴더**

저번주에 util 폴더로 했는데 util에는 상수를 넣는 게 아닌 것 같다..  
그래서 폴더명을 constants로 바꿨다

- AppConstants.js

  ```jsx
  const AppConstants = Object.freeze({
    MINIMUM: 0,
    MAXIMUM: 9,
    FORWARD_CONDITION: 4,
  });

  export default AppConstants;
  ```

- Messages.js

  ```jsx
  const Messages = Object.freeze({
    INPUT_NAME:
      "경주할 자동차 이름을 입력하세요.(이름은 쉼표(,) 기준으로 구분)\n",
    INPUT_NUMBER: "시도할 횟수는 몇 회인가요?\n",
    RESULT: "실행결과",
    WINNER: "최종 우승자 : ",

    ERROR: {
      INVALID_INPUT_NUMBER: "[ERROR] 숫자를 중복없이 입력해주세요.",
    },
  });
  export default Messages;
  ```

## <mark style='background-color: #ffdce0'>2023.10.29(일)</mark>

### 💻 개발

**App.js**

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>- 입력을 받고 ValidateInput 클래스를 통해 유효성 검사를 한다.</div>
    <div>- 유효성 검사를 통과했다면 RacingCar를 실행 시킨다.</div>
  </p>
</div>

```jsx
import { Console } from "@woowacourse/mission-utils";
import { RacingCar } from "./components/RacingCar.js";
import { ValidateInput } from "./components/ValidateInput.js";
import Messages from "./constants/Messages.js";

class App {
  async play() {
    const validator = new ValidateInput();

    const inputName = await Console.readLineAsync(Messages.INPUT_NAME);
    const inputNameArray = inputName.split(",");
    validator.name(inputName, inputNameArray);

    const inputNumber = Number(
      await Console.readLineAsync(Messages.INPUT_NUMBER)
    );
    validator.number(inputNumber);

    const racingCar = new RacingCar(inputNameArray, inputNumber);
    racingCar.output();
  }
}

export default App;
```

---

**components 폴더**

**1\. ValidateInput.js**

```jsx
import Messages from "../constants/Messages.js";

export class ValidateInput {
  name(inputName, inputNameArray) {
    if (!inputName.length) {
      throw new Error(Messages.ERROR.INVALID_NAME_NULL);
    }
    for (let i = 0; i < inputNameArray.length; i++) {
      if (inputNameArray[i].includes(" ")) {
        throw new Error(Messages.ERROR.INVALID_NAME_NOT_SPACE);
      }
      if (inputNameArray[i].length > 5) {
        throw new Error(Messages.ERROR.INVALID_NAME_LENGTH_LIMIT);
      }
    }
  }
  number(inputNumber) {
    if (!Number.isInteger(inputNumber) || inputNumber < 1) {
      throw new Error(Messages.ERROR.INVALID_NUMBER_INTEGER);
    }
  }
}
```

**2\. CreateNumber.js**

```jsx
import { Random } from "@woowacourse/mission-utils";
import AppConstants from "../constants/AppConstants.js";

export class CreateNumber {
  constructor() {
    this.randomNumber = Random.pickNumberInRange(
      AppConstants.MINIMUM,
      AppConstants.MAXIMUM
    );
  }
}
```

**3\. RacingCar.js**

```jsx
import { Console } from "@woowacourse/mission-utils";
import { CreateNumber } from "./CreateNumber.js";
import AppConstants from "../constants/AppConstants.js";
import Messages from "../constants/Messages.js";

export class RacingCar {
  constructor(inputNameArray, inputNumber) {
    this.inputNameArray = inputNameArray;
    this.inputNumber = inputNumber;
  }

  output() {
    const nameArrayLength = this.inputNameArray.length;
    const forwardCounts = new Array(nameArrayLength).fill(0);
    // 실행 결과
    Console.print(Messages.OUTPUT);
    for (let i = 0; i < this.inputNumber; i++) {
      for (let j = 0; j < nameArrayLength; j++) {
        forwardCounts[j] += this.isForward();
        Console.print(
          `${this.inputNameArray[j]} : ${AppConstants.FORWARD.repeat(
            forwardCounts[j]
          )}`
        );
      }
      Console.print("");
    }
    // 최종 우승자
    const maxValue = Math.max(...forwardCounts);
    const winners = this.inputNameArray.filter(
      (_, i) => forwardCounts[i] === maxValue
    );
    Console.print(winners.map((winner) => winner).join(", "));
  }

  isForward() {
    const randomNumber = new CreateNumber().randomNumber;
    return randomNumber >= AppConstants.FORWARD_CONDITION;
  }
}
```

## <mark style='background-color: #ffdce0'>2023.10.30(월)</mark>

### 📝 공부한 것

**Jest 테스트 도구 사용법**

**1\. 기본 문법**

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>- 테스트 설명: 명확하고 간결한 설명이 좋다.</div>
    <div>- 검증 대상: 객체, 함수, 메서드, 표현식 등이 올 수 있다. 여기에 코드를 삽입하여 실제 동장을 테스트 한다.</div>
    <div>- toXxx: Jest의 다양한 매처 중 하나를 나타낸다. (toBe, toEqual, toContain, toMatch, toBeTruthy, toBeFalsy, toThrow 등)</div>
    <div>- 기대 결과: 테스트가 예상한 결과를 나타낸다.</div>
  </p>
</div>

```
test("테스트 설명", () => {
  expect("검증 대상").toXxx("기대 결과");
});
```

---

**2\. 예시**

```jsx
test("split 메서드로 주어진 값을 구분", () => {
  const input = "pobi,woni,java";
  const result = input.split(",");
  expect(result).toContain("pobi", "woni", "java");
  expect(result).toContainEqual("java", "woni", "pobi");
});
```

---

### 🎯 최종 기능 명세서

```markdown
🎯 기능 명세서
1\. 경주할 자동차 이름과 횟수 입력받기

- 이름 예외처리
  - 무조건 입력해야 한다.
  - 이름엔 띄어쓰기가 들어갈 수 없다.
  - 쉼표가 한번에 두개 이상 찍히거나 제일 앞이나 뒤에 찍힐 수 없다.
  - 이름은 5자 이하여야한다.
- 횟수 예외처리
  - 1이상의 정수만 입력해야 한다.

2\. 전진할지 정하기

- 0에서 9사이의 무작위 값을 구한다.
- 0, 0, 4 라는 숫자는 상수로 관리한다.

3\. 우승자 출력하기

- 여러 명일 경우 쉼표를 이용하여 구분한다.
- ', '를 하면 된다.(뒤에 띄어쓰기 주의)

※ 추가 확인 사항

- indent(인덴트, 들여쓰기) depth를 3이 넘지 않도록 구현한다.
- Jest를 이용하여 본인이 정리한 기능 목록이 정상 동작함을 테스트 코드로 확인한다.

※ 만들어야할 것

- 상수, 메세지 관리 파일
- 숫자 랜덤 추출 클래스
- 레이싱 과정을 출력하는 클래스
- 입력값 유효성 검사 클래스
```

---

### 💻 최종 예외처리 코드

**1\. Message.js**

```jsx
const Messages = Object.freeze({
  INPUT_NAME:
    "경주할 자동차 이름을 입력하세요.(이름은 쉼표(,) 기준으로 구분)\n",
  INPUT_NUMBER: "시도할 횟수는 몇 회인가요?\n",
  OUTPUT: "\n실행결과",
  WINNER: "최종 우승자 : ",

  ERROR: {
    INVALID_NAME_NULL: "[ERROR] 자동차 이름을 입력해주세요.",
    INVALID_NAME_NOT_SPACE:
      "[ERROR] 자동차 이름엔 띄어쓰기가 들어갈 수 없습니다.",
    INVALID_NAME_COMMA: "[ERROR] 쉼표의 위치를 다시 확인해주세요.",
    INVALID_NAME_LENGTH_LIMIT:
      "[ERROR] 자동차 이름은 5자 이하로만 입력해주세요.",
    INVALID_NAME_NO_SAME: "[ERROR] 자동차 이름은 중복될 수 없습니다.",
    INVALID_NUMBER_INTEGER: "[ERROR] 횟수는 1이상의 정수만 가능합니다.",
  },
});
export default Messages;
```

**2\. ValidateInput.js**

```jsx
import Messages from "../constants/Messages.js";

export class ValidateInput {
  name(inputName, inputNameArray) {
    if (!inputName.length) {
      throw new Error(Messages.ERROR.INVALID_NAME_NULL);
    }
    for (let i = 0; i < inputNameArray.length; i++) {
      if (inputNameArray[i].includes(" ")) {
        throw new Error(Messages.ERROR.INVALID_NAME_NOT_SPACE);
      }
      if (inputNameArray[i].length == 0) {
        throw new Error(Messages.ERROR.INVALID_NAME_COMMA);
      }
      if (inputNameArray[i].length > 5) {
        throw new Error(Messages.ERROR.INVALID_NAME_LENGTH_LIMIT);
      }
    }
    if (new Set(inputNameArray).size !== inputNameArray.length) {
      throw new Error(Messages.ERROR.INVALID_NAME_NO_SAME);
    }
  }
  number(inputNumber) {
    if (!Number.isInteger(inputNumber) || inputNumber < 1) {
      throw new Error(Messages.ERROR.INVALID_NUMBER_INTEGER);
    }
  }
}
```

### ✅ 테스트

**1\. App.js**

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>- 처음엔 `await expect(app.play()).rejects.toThrow("[ERROR]");` 이런 코드로 되어있다.</div>
    <div>- 하지만 이름과 횟수 입력을 구분해야한다. 따라서 `app.play()` 가 아닌 각각의 입력 메소드를 불러와야 한다.</div>
    <div>- 나의 App.js 코드는 이름과 횟수 입력을 메소드로 구현하지 않았기 때문에 이 부분부터 고쳐야 했다.</div>
    <div>- 다음은 이름과 횟수 입력을 메소드로 고친 코드이다.</div>
  </p>
</div>

```jsx
import { Console } from "@woowacourse/mission-utils";
import { RacingCar } from "./components/RacingCar.js";
import { ValidateInput } from "./components/ValidateInput.js";
import Messages from "./constants/Messages.js";

class App {
  async play() {
    const inputNameArray = await this.InputName();
    const inputNumber = await this.InputNumber();

    const racingCar = new RacingCar(inputNameArray, inputNumber);
    racingCar.output();
  }

  async InputName() {
    const inputName = await Console.readLineAsync(Messages.INPUT_NAME);
    const inputNameArray = inputName.split(",");
    new ValidateInput().name(inputName, inputNameArray);
    return inputNameArray;
  }

  async InputNumber() {
    const inputNumber = Number(
      await Console.readLineAsync(Messages.INPUT_NUMBER)
    );
    new ValidateInput().number(inputNumber);
    return inputNumber;
  }
}

export default App;
```

---

**2\. InputTest.js**

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>자동차 이름 유효성 검사를 하고 싶을 땐 `expect(app.InputName())` 이렇게 부르고,</div>
    <div>횟수 유효성 검사를 하고 싶을 땐 `expect(app.InputNumber())` 로 부르면 된다.</div>
  </p>
</div>

```jsx
import App from "../src/App.js";
import { MissionUtils } from "@woowacourse/mission-utils";

const mockQuestions = (inputs) => {
  MissionUtils.Console.readLineAsync = jest.fn();

  MissionUtils.Console.readLineAsync.mockImplementation(() => {
    const input = inputs.shift();
    return Promise.resolve(input);
  });
};

describe("문자열 테스트", () => {
  // split ','로 구분
  test("split 메서드로 주어진 값을 구분", () => {
    const input = "pobi,woni,java";
    const result = input.split(",");
    expect(result).toContain("pobi", "woni", "java");
    expect(result).toContainEqual("java", "woni", "pobi");
  });

  test("split 메서드로 구분자가 포함되지 않은 경우 값을 그대로 반환", () => {
    const input = "pobi";
    const result = input.split(",");
    expect(result).toContain("pobi");
  });

  // 자동차 이름 유효성 검사
  test.each([[[""]]])(
    "자동차 이름이 아무것도 입력되지 않았을 경우",
    async (inputs) => {
      mockQuestions(inputs);
      const app = new App();
      await expect(app.InputName()).rejects.toThrow(
        "[ERROR] 자동차 이름을 입력해주세요"
      );
    }
  );

  test.each([
    [["pobi, woni,java"]],
    [["pobi,woni,ja va"]],
    [["pobi ,woni,java"]],
  ])("자동차 이름에 띄어쓰기가 있을 경우", async (inputs) => {
    mockQuestions(inputs);
    const app = new App();
    await expect(app.InputName()).rejects.toThrow(
      "[ERROR] 자동차 이름엔 띄어쓰기가 들어갈 수 없습니다."
    );
  });

  test.each([[["pobi,,woni,java"]], [["pobi,woni,"]], [[",pobi,woni,java"]]])(
    "쉼표를 잘못 적었을 경우",
    async (inputs) => {
      mockQuestions(inputs);
      const app = new App();
      await expect(app.InputName()).rejects.toThrow(
        "[ERROR] 쉼표의 위치를 다시 확인해주세요."
      );
    }
  );

  test.each([[["pobiwoni,java"]], [["pobiwoni"]], [["pobi,wonijava"]]])(
    "자동차 이름이 5자 초과일 경우",
    async (inputs) => {
      mockQuestions(inputs);
      const app = new App();
      await expect(app.InputName()).rejects.toThrow(
        "[ERROR] 자동차 이름은 5자 이하로만 입력해주세요."
      );
    }
  );

  test.each([[["pobi,woni,pobi"]], [["a,a,a"]]])(
    "자동차 이름이 중복될 경우",
    async (inputs) => {
      mockQuestions(inputs);
      const app = new App();
      await expect(app.InputName()).rejects.toThrow(
        "[ERROR] 자동차 이름은 중복될 수 없습니다."
      );
    }
  );

  // 횟수 유효성 검사
  test.each([[["0"]], [["4.5"]], [["a"]], [["1 2"]]])(
    "횟수가 0 이하거나 정수가 아닐 경우 예외 처리",
    async (inputs) => {
      mockQuestions(inputs);
      const app = new App();
      await expect(app.InputNumber()).rejects.toThrow(
        "[ERROR] 횟수는 1이상의 정수만 가능합니다."
      );
    }
  );
});
```

---

**3\. RacingCarTest.js**

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>우승자가 여러 명일 때 예시 하나만 만들었다.</div>
  </p>
</div>

```jsx
import App from "../src/App.js";
import { MissionUtils } from "@woowacourse/mission-utils";

const mockQuestions = (inputs) => {
  MissionUtils.Console.readLineAsync = jest.fn();

  MissionUtils.Console.readLineAsync.mockImplementation(() => {
    const input = inputs.shift();
    return Promise.resolve(input);
  });
};

const mockRandoms = (numbers) => {
  MissionUtils.Random.pickNumberInRange = jest.fn();
  numbers.reduce((acc, number) => {
    return acc.mockReturnValueOnce(number);
  }, MissionUtils.Random.pickNumberInRange);
};

const getLogSpy = () => {
  const logSpy = jest.spyOn(MissionUtils.Console, "print");
  logSpy.mockClear();
  return logSpy;
};

// 3명 모두 우승
describe("자동차 경주 게임", () => {
  test("전진-정지", async () => {
    // given
    const MOVING_FORWARD = 9;
    const STOP = 0;
    const inputs = ["pobi,woni,jun", "3"];
    const outputs = [
      "pobi : -",
      "woni : ",
      "jun : -",
      "pobi : --",
      "woni : -",
      "jun : --",
      "pobi : --",
      "woni : --",
      "jun : --",
    ];
    const randoms = [
      MOVING_FORWARD,
      STOP,
      MOVING_FORWARD,
      MOVING_FORWARD,
      MOVING_FORWARD,
      MOVING_FORWARD,
      STOP,
      MOVING_FORWARD,
      STOP,
    ];
    const logSpy = getLogSpy();

    mockQuestions(inputs);
    mockRandoms([...randoms]);

    // when
    const app = new App();
    await app.play();

    // then
    outputs.forEach((output) => {
      expect(logSpy).toHaveBeenCalledWith(expect.stringContaining(output));
    });
  });
});
```
