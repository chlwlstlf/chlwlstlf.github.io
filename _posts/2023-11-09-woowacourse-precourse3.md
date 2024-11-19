---
layout: single
title: "[우테코]프리코스 3주차"
categories: woowacourse
toc: true
toc_sticky: true
---

# 🎱 로또 게임

<mark style='color: red'>※ 진블로그는 링크가 있는 사람만 조회할 수 있어서 저만의 기록 공간으로 활용하고 있습니다.</mark>

## <mark class="pink">2023.11.03(금)</mark>

### 🧐 2주차 공통 피드백

[2주차 공통 피드백](https://docs.google.com/document/d/1mbmVtUvdD54qPcgeDtoFYOO_Y2_CZDkBZ5uSqPimZ-o/edit){: .btn .btn--primary}

- 해당 프로젝트가 어떠한 프로젝트이며, 어떤 기능을 담고 있는지 작성하자.
- 구현해야 할 기능 목록을 정리하는 데 집중한다. **정상적인 경우도 중요하지만, 예외적인 상황도 기능 목록에 정리**한다. 특히 예외 상황은 시작 단계에서 모두 찾기 힘들기 때문에 기능을 구현하면서 계속해서 추가해 나간다.
- 클래스는 필드, 생성자, 메서드 순으로 작성한다.
- 한 함수가 한 가지 기능만 담당하게 한다. 함수에서 안내 문구 출력, 사용자 입력, 유효값 검증 등 여러 일을 하고 있다면 이를 적절하게 분리한다.
- 함수의 길이를 15라인을 넘어가지 않도록 구현하며 함수를 분리하는 의식적인 연습을 할 수 있다.
- JS에서는 클래스 말고도 객체를 만드는 방법은 여러 가지가 있다. Object도 사용해보자.
- 테스트의 중요한 목적 중 하나는 내가 작성하는 코드에 대해 빠르게 피드백을 받는 것이다.

```bash
작은 단위의 테스트

- 무작위 값이 4 이상이면 자동차가 전진한다.
- 무작위 값이 3 이하이면 자동차가 전진하지 않는다.
```

### 🚀 기능 요구사항

<mark class="yellow">로또 게임 기능을 구현한다.</mark>

- 로또 번호의 숫자 범위는 1~45까지이다.
- 1개의 로또를 발행할 때 중복되지 않는 6개의 숫자를 뽑는다.
- 당첨 번호 추첨 시 중복되지 않는 숫자 6개와 보너스 번호 1개를 뽑는다.
- 당첨은 1등부터 5등까지 있다. 당첨 기준과 금액은 아래와 같다.

  - 1등: 6개 번호 일치 / 2,000,000,000원
  - 2등: 5개 번호 + 보너스 번호 일치 / 30,000,000원
  - 3등: 5개 번호 일치 / 1,500,000원
  - 4등: 4개 번호 일치 / 50,000원
  - 5등: 3개 번호 일치 / 5,000원

- 로또 구입 금액을 입력하면 구입 금액에 해당하는 만큼 로또를 발행해야 한다.
- 로또 1장의 가격은 1,000원이다.
- 당첨 번호와 보너스 번호를 입력받는다.
- 사용자가 구매한 로또 번호와 당첨 번호를 비교하여 당첨 내역 및 수익률을 출력하고 로또 게임을 종료한다.
- 사용자가 잘못된 값을 입력할 경우 `throw`문을 사용해 예외를 발생시킨다. 그런 다음, "[ERROR]"로 시작하는 에러 메시지를 출력하고 해당 부분부터 입력을 다시 받는다.  
  `예시) [ERROR] 숫자가 잘못된 형식입니다.`

---

<mark class="yellow">입출력 순서</mark>

**1\. 구입 금액 입력**  
구입 금액은 1,000원 단위로 입력 받으며 1,000원으로 나누어 떨어지지 않는 경우 예외 처리한다.

```
구입금액을 입력해 주세요.
8000
```

**2\. 구매 개수와 로또 번호 출력**  
랜덤, 오름차순으로 정렬하여 보여준다.

```
8개를 구매했습니다.
[8, 21, 23, 41, 42, 43]
[3, 5, 11, 16, 32, 38]
[7, 11, 16, 35, 36, 44]
[1, 8, 11, 31, 41, 42]
[13, 14, 16, 38, 42, 45]
[7, 11, 30, 40, 42, 43]
[2, 13, 22, 32, 38, 45]
[1, 3, 5, 14, 22, 45]
```

**3\. 당첨 번호 입력**  
[ERROR] 로또 번호는 1부터 45 사이의 숫자여야 합니다.

```
당첨 번호를 입력해 주세요.
1,2,3,4,5,6
```

**4\. 보너스 번호 입력**  
[ERROR] 로또 번호는 1부터 45 사이의 숫자여야 합니다.

```
보너스 번호를 입력해 주세요.
7
```

**5\. 당첨 내역 출력**  
수익률은 소수점 둘째 자리에서 반올림한다.

```
당첨 통계
---
3개 일치 (5,000원) - 1개
4개 일치 (50,000원) - 0개
5개 일치 (1,500,000원) - 0개
5개 일치, 보너스 볼 일치 (30,000,000원) - 0개
6개 일치 (2,000,000,000원) - 0개
총 수익률은 62.5%입니다.
```

### 🎯 프로그래밍 요구사항

**※ 기본 요구 사항**

- indent(인덴트, 들여쓰기) depth를 3이 넘지 않도록 구현한다. 2까지만 허용한다.
  - 예를 들어 while문 안에 if문이 있으면 들여쓰기는 2이다.
  - 힌트: indent(인덴트, 들여쓰기) depth를 줄이는 좋은 방법은 함수(또는 메소드)를 분리하면 된다.
- Jest를 이용하여 본인이 정리한 기능 목록이 정상 동작함을 테스트 코드로 확인한다.
  - 테스트 도구 사용법이 익숙하지 않다면 `__tests__/StringTest.js`를 참고하여 학습한 후 테스트를 구현한다.

---

**※ 추가된 요구 사항**

- 함수(또는 메서드)의 길이가 15라인을 넘어가지 않도록 구현한다.
  - 함수(또는 메서드)가 한 가지 일만 잘 하도록 구현한다.
- else를 지양한다.
  - 힌트: if 조건절에서 값을 return하는 방식으로 구현하면 else를 사용하지 않아도 된다.
  - 때로는 if/else, switch문을 사용하는 것이 더 깔끔해 보일 수 있다. 어느 경우에 쓰는 것이 적절할지 스스로 고민해 본다.
- 도메인 로직에 단위 테스트를 구현해야 한다. 단, UI(Console.readLineAsync, Console.print) 로직에 대한 단위 테스트는 제외한다.
  - 핵심 로직을 구현하는 코드와 UI를 담당하는 로직을 구분한다.
  - 단위 테스트 작성이 익숙하지 않다면 `__tests__/LottoTest.js`를 참고하여 학습한 후 테스트를 구현한다.

---

**Lotto 클래스**

- 제공된 `Lotto` 클래스를 활용해 구현해야 한다.
- `numbers`의 `#` prefix를 변경할 수 없다.
- `Lotto`에 필드를 추가할 수 없다.

```jsx
class Lotto {
  #numbers;

  constructor(numbers) {
    this.#validate(numbers);
    this.#numbers = numbers;
  }

  #validate(numbers) {
    if (numbers.length !== 6) {
      throw new Error("[ERROR] 로또 번호는 6개여야 합니다.");
    }
  }

  // TODO: 추가 기능 구현
}
```

---

### 📝 공부한 것

**1️⃣ 클래스**

**1\. 클래스 정의**  
클래스를 정의할 때는 **`class`** 키워드를 사용합니다. 클래스 이름은 일반적으로 대문자로 시작하며, 관례상 파스칼 케이스(PascalCase)를 따릅니다.

```jsx
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  sayHello() {
    console.log(`Hello, my name is ${this.name}.`);
  }
}
```

**2\. 생성자 메서드**  
클래스의 생성자 메서드는 **`constructor`** 메서드로 객체를 초기화하고 속성을 설정합니다.

**3\. 메서드**  
클래스 내에서 메서드를 정의하여 객체의 동작을 정의할 수 있습니다.

**4\. 인스턴스 생성**  
클래스를 기반으로 객체(인스턴스)를 생성할 때는 **`new`** 키워드를 사용합니다.

```jsx
const person1 = new Person("Alice", 30);
const person2 = new Person("Bob", 25);
```

**5\. 상속**  
클래스를 상속하여 새로운 클래스를 만들 수 있습니다. 상속을 통해 코드 재사용성을 높일 수 있습니다.

```jsx
class Student extends Person {
  constructor(name, age, studentId) {
    super(name, age); // 부모 클래스 생성자 호출
    this.studentId = studentId;
  }

  study() {
    console.log(`${this.name} is studying.`);
  }
}
```

**6\. 정적 메서드**  
클래스의 정적 메서드는 클래스 자체에 속하며 인스턴스와 무관한 작업을 수행합니다.

```jsx
class MathUtils {
  static add(a, b) {
    return a + b;
  }
}

const sum = MathUtils.add(5, 3);
```

**7\. 접근 제어자**  
최신 JavaScript 버전(ECMAScript 6+)에서는 클래스 필드와 메서드에 대한 접근 제어자를 사용할 수 있습니다. 이를 통해 속성과 메서드의 가시성을 제어할 수 있습니다.  
여기서 `constructor`는 필드가 비공개(private)임을 나타내며, 외부에서 직접 접근할 수 없습니다.

```jsx
class Car {
  #speed = 0; // 비공개 필드

  accelerate() {
    this.#speed += 10;
  }

  getSpeed() {
    return this.#speed;
  }
}
```

---

**2️⃣ 접근 제어자**

**접근 제어자를 사용하는 이유**

**1\. 캡슐화**  
접근 제어자를 사용하면 클래스의 내부 상태를 숨기고 외부에서 직접 접근하는 것을 방지할 수 있습니다. 이는 클래스의 내부 구현을 보호하고 오용을 방지합니다.

**2\. 유효성 검사 및 로직 추가**  
`get` 및 `set` 메서드를 사용하여 속성에 접근할 때 값을 검증하거나 특정 로직을 추가할 수 있습니다. 예를 들어, 속성 값을 설정할 때 특정 범위 내의 값만 허용하도록 제한할 수 있습니다.

**3\. 상호작용 인터페이스**  
`get` 및 `set` 메서드를 사용하면 클래스의 사용자(외부 코드)와 클래스 사이에 일관된 인터페이스를 제공할 수 있습니다. 사용자는 클래스 멤버에 접근할 때 메서드를 호출하는 방식으로 일관된 API를 사용할 수 있습니다.

**4\. 미래 확장성**  
접근 제어자를 사용하면 클래스의 내부 구현을 변경하지 않고도 외부 API를 확장하거나 수정할 수 있습니다. 이는 코드 유지 및 확장을 용이하게 만듭니다.

---

**접근 제어자 사용 방법**

**1\. `get` 메서드**

- `get` 메서드는 클래스 내에서 속성 값을 읽을 때 사용됩니다.
- 이 메서드를 사용하면 속성 값을 읽을 때 사용하는 문법은 메서드 호출처럼 보이지만 실제로는 속성 값을 반환하는 역할을 합니다.
- `getName` 메서드를 통해 `#name` 필드 값을 읽을 수 있습니다.

```jsx
class Person {
  #name = "Alice";

  get getName() {
    return this.#name;
  }
}

const person = new Person();
console.log(person.getName); // 'Alice'
```

**2\. `set` 메서드**

- `set` 메서드는 클래스 내에서 속성 값을 설정 또는 변경할 때 사용됩니다.
- 이 메서드를 사용하면 속성에 값을 할당할 때 사용하는 문법은 메서드 호출처럼 보이지만 실제로는 속성 값을 설정하는 역할을 합니다.
- `setName` 메서드를 통해 `#name` 필드 값을 변경할 수 있습니다.

```jsx
class Person {
  #name = "Alice";

  set setName(newName) {
    this.#name = newName;
  }
}

const person = new Person();
person.setName = "Bob";
console.log(person.getName); // 'Bob'
```

## <mark class="pink">2023.11.04(토)</mark>

### 🛠️ 고려할 점

- **for문을 되도록이면 쓰지 말자.**  
  JS의 고차 함수로 대체할 수 있다. forEach, reduce 등을 활용할 수 있다.

- **함수(또는 메서드)가 한 가지 일만 잘 하도록 구현하자.**

- **코드 컨벤션을 고려하자.**  
  prettier로 EOL를 설정하고 eslint와 airbnb를 설치한다. 1주차 때 package.json을 변경하면 안 된다는 조건이 있어서 다시 uninstall 했는데 그냥 설치 후 package.json은 커밋을 안 하면 되는 거였다. 그래서 이번 주차에는 설치를 한 후 개발을 하려고 한다.

  **참고한 블로그**  
  👉🏻 [https://velog.io/@2wndrhs/알아두면-쓸데있는-ESLint-Prettier-설정-방법](https://velog.io/@2wndrhs/%EC%95%8C%EC%95%84%EB%91%90%EB%A9%B4-%EC%93%B8%EB%8D%B0%EC%9E%88%EB%8A%94-ESLint-Prettier-%EC%84%A4%EC%A0%95-%EB%B0%A9%EB%B2%95)

  **1\. eslint 설치**

  ```bash
  C:\Users\LG\Desktop\취업\15_우테코\javascript-lotto-6>npm init @eslint/config
  Need to install the following packages:
    @eslint/create-config@0.4.6
  Ok to proceed? (y) y
  √ How would you like to use ESLint? · style
  √ What type of modules does your project use? · commonjs
  √ Which framework does your project use? · none
  √ Does your project use TypeScript? · No / Yes
  √ Where does your code run? · node
  √ How would you like to define a style for your project? · guide
  √ Which style guide do you want to follow? · airbnb
  √ What format do you want your config file to be in? · JavaScript
  Checking peerDependencies of eslint-config-airbnb-base@latest
  Local ESLint installation not found.
  The config that you've selected requires the following dependencies:

  eslint-config-airbnb-base@latest eslint@^7.32.0 || ^8.2.0 eslint-plugin-import@^2.25.2
  √ Would you like to install them now? · No / Yes
  √ Which package manager do you want to use? · npm
  Installing eslint-config-airbnb-base@latest, eslint@^7.32.0 || ^8.2.0, eslint-plugin-import@^2.25.2

  added 147 packages, removed 1 package, changed 1 package, and audited 527 packages in 7s

  103 packages are looking for funding
    run `npm fund` for details

  found 0 vulnerabilities
  Successfully created .eslintrc.cjs file in C:\Users\LG\Desktop\취업\15_우테코\javascript-lotto-6
  ```

  **2\. prettier 설치**

  ```bash
  C:\Users\LG\Desktop\취업\15_우테코\javascript-lotto-6>npm i -D prettier eslint-config-prettier

  added 2 packages, and audited 529 packages in 1s

  104 packages are looking for funding
    run `npm fund` for details

  found 0 vulnerabilities
  ```

  .prettierrc 파일

  ```json
  {
    "singleQuote": true,
    "semi": true,
    "useTabs": false,
    "tabWidth": 2,
    "trailingComma": "all",
    "printWidth": 80,
    "bracketSpacing": true,
    "arrowParens": "always",
    "endOfLine": "auto"
  }
  ```

  .eslintrc.cjs 파일

  ```jsx
  module.exports = {
    env: {
      commonjs: true,
      es2021: true,
      node: true,
    },
    extends: ["airbnb-base", "prettier"], //이 부분 수정
    overrides: [
      {
        env: {
          node: true,
        },
        files: [".eslintrc.{js,cjs}"],
        parserOptions: {
          sourceType: "script",
        },
      },
    ],
    parserOptions: {
      ecmaVersion: "latest",
    },
    rules: {},
  };
  ```

  **3\. `Extensions` 탭에서 `ESLint`와 `Prettier` 확장 프로그램을 설치**

  **4\. setting.json 파일 생성**

  ```bash
  {
    "editor.codeActionsOnSave": { "source.fixAll.eslint": true },
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "editor.formatOnSave": true
  }
  ```

  **5\. .eslintrc.cjs의 rules에 조건 추가**

  - 함수(또는 메서드)의 길이가 15라인을 넘어가지 않도록 구현한다.
  - indent(인덴트, 들여쓰기) depth를 3이 넘지 않도록 구현한다. 2까지만 허용한다.

  ```jsx
  rules: {
    'max-lines-per-function': ['error', { max: 15 }],
    'max-depth': ['error', 2],
  },
  ```

  **6\. Jest를 허용한다**

  ```jsx
  env: {
    commonjs: true,
    es2021: true,
    node: true,
    jest: true, //이 부분
  },
  ```

### 💻 개발 시작

이번엔 MVC 패턴을 도입하려 한다. 폴더 구조를 정하기 전 큰 틀을 적어 보았다.

**1\. 모델 (Model)**

- 모델은 데이터와 비즈니스 로직을 포함합니다. 이 부분에서 데이터를 저장하고 관리하며 데이터를 조작하거나 비즈니스 로직을 수행합니다.
- 로짐에서는 로또 티켓 생성, 당첨 결과 계산 등의 비즈니스 로직이 모델에 속합니다.
- 모델은 주로 데이터의 상태를 유지하고 업데이트하는 역할을 합니다.

**2\. 뷰 (View)**

- 뷰는 사용자에게 데이터를 시각적으로 표시하는 역할을 합니다. 이 부분에서는 화면 출력, 메시지 표시 등의 사용자 인터페이스와 관련된 작업을 담당합니다.
- 로또 게임에서는 로또 티켓 출력, 당첨 결과 출력 등이 뷰에 해당합니다.
- 뷰는 주로 데이터의 시각적 표현을 담당하고 사용자와의 상호작용을 처리합니다.

**3\. 컨트롤러 (Controller)**

- 컨트롤러는 모델과 뷰 간의 중개자 역할을 합니다. 사용자의 입력을 받아 모델을 업데이트하고, 모델의 상태를 뷰에 표시하도록 조정합니다.
- 로또 게임에서는 사용자 입력 처리, 모델과 뷰의 연결 및 게임 논리 실행이 컨트롤러에 해당합니다.
- 컨트롤러는 주로 사용자와의 상호작용과 전체적인 프로그램 흐름을 관리합니다.

---

**※ 폴더, 파일 정하기**

MVC 패턴에 맞추어 폴더를 대략적으로 나눠보고 그 안의 파일들을 적어보았다.

**1\. constants 폴더**

- errorMessage
- gameMessage

**2\. models 폴더**

- RandomLotto(랜덤으로 생성된 로또)
- Lotto(당첨 로또 번호)
- Validate

**3\. views 폴더**

- InputView(구입 금액, 당첨 번호, 보너스 번호 입력)
- OutputView(구매개수, 랜덤 로또 번호, 당첨 내역 출력)

**4\. controller 폴더**

- Controller(게임 진행)

---

**※ 개발 순서 정하기**

1\. 구입 금액 입력, 유효성 검사

2\. 구매 개수 출력, 랜덤 로또 만든 후 리턴, 랜덤 로또 번호 출력

3\. 당첨 번호 입력, 유효성 검사, 로또에서 배열로 바꾼 후 유효성 검사, 당첨 번호 리턴

4\. 보너스 번호 입력, 유효성 검사, 보너스 번호 리턴

5\. 당첨 내역 출력

## <mark class="pink">2023.11.05(일)</mark>

### 🛠️ eslint 적용된 내용

- **`export default class` 로 선언해야 한다.**

  ```jsx
  export default class Validate {
    //코드
  }
  ```

- **확장자를 명시하도록 바꿔야 한다.**

  eslint가 적용되어 확장자를 없애는 코드를 짰는데 이러면 터미널에서 파일을 인식하지 못 한다.

  ```jsx
  import App from "./App";
  ```

  .eslintrc.cjs 코드 추가

  ```jsx
  rules: {
      'max-lines-per-function': ['error', { max: 15 }],
      'class-methods-use-this': 'off',
      'import/extensions': 'off', //이 부분 추가
    },
  ```

  ```jsx
  import App from "./App.js"; //확장자명 추가
  ```

### 🎯 0. 컨트롤러 추가

- **App.js**

  ```jsx
  import Controller from "./controller/Controller.js";

  export default class App {
    constructor() {
      this.controller = new Controller();
    }

    async play() {
      await this.controller.start();
    }
  }
  ```

- **controller > Controller.js**

  ```jsx
  export default class Controller {
    #money;

    #quantity;

    #randomLottos;

    #winningNumbers;

    #bonusNumber;
  }
  ```

### 🎯 0. constants 폴더 추가

- **constant.js**

  ```jsx
  const constant = Object.freeze({
    THOUSAND: 1000,
    MINIMUM: 1,
    MAXIMUM: 45,
    COUNT: 6,
  });
  export default constant;
  ```

- **gameMessage.js**

  ```jsx
  const gameMessage = Object.freeze({
    INPUT: {
      MONEY: "구입금액을 입력해 주세요.\n",
      WINNING: "\n당첨 번호를 입력해 주세요.\n",
      BONUS: "\n보너스 번호를 입력해 주세요.",
    },

    OUTPUT: {
      QUANTITY: (quantity) => `\n${quantity}개를 구매했습니다.`,
      RESULT_TEXT: "당첨 통계\n---",
      RESULT: (match, reward, quantity) =>
        `${match} (${reward}원) - ${quantity}개`,
      INCOME: (rate) => `총 수익률은 ${rate}%입니다.`,
    },
  });
  export default gameMessage;
  ```

- **errorMessage.js**

  ```jsx
  const errorMessage = Object.freeze({
    NOT_NULL: "[ERROR] 필수로 입력해주세요.\n",
    ONLY_NUMBER: "[ERROR] 숫자만 입력해주세요.\n",
    OUT_OF_RANGE: "[ERROR] 로또 번호는 1부터 45 사이의 숫자입니다.\n",

    MONEY: {
      DIVISIBLE: "[ERROR] 1000원 단위로 입력해주세요.\n",
    },

    WINNING: {
      NOT_SPACE: "[ERROR] 중간에 띄어쓰기를 빼고 입력해주세요.\n",
      COMMA: "[ERROR] 쉼표의 위치를 다시 확인해주세요.\n",
      SIX_NUMBERS: "[ERROR] 숫자 6개가 입력되었는 지 확인해주세요.\n",
    },
  });
  export default errorMessage;
  ```

### 🎯 1. 구입 금액 입력

- **controller > Controller.js**

  ```jsx
  import InputView from "../views/InputView.js";
  import Validate from "../models/Validate.js";

  export default class Controller {
    #money;

    #quantity;

    #randomLottos;

    #winningNumbers;

    #bonusNumber;

    constructor() {
      this.validate = new Validate();
    }

    async start() {
      this.#money = await InputView.money();
      this.validate.money(this.#money);
    }
  }
  ```

- **views > InputView.js**

  ```jsx
  import { Console } from "@woowacourse/mission-utils";
  import gameMessage from "../constants/gameMessages.js";

  const InputView = {
    async money() {
      const inputValue = await Console.readLineAsync(gameMessage.INPUT.MONEY);
      return inputValue;
    },
  };

  export default InputView;
  ```

- **util > Validate.js**

  ```jsx
  import errorMessage from "../constants/errorMessage.js";

  const Validate = {
    money(inputMoney) {
      this.notNull(inputMoney);
      this.onlyNumber(inputMoney);
      this.divisible(inputMoney);
    },

    notNull(inputValue) {
      if (!inputValue.length) {
        throw new Error(errorMessage.NOT_NULL);
      }
    },

    onlyNumber(inputValue) {
      const numberRegExp = /^[0-9]+$/;
      if (!numberRegExp.test(inputValue)) {
        throw new Error(errorMessage.ONLY_NUMBER);
      }
    },

    divisible(inputValue) {
      const number = Number(inputValue);
      if (number === 0 || number % 1000 !== 0) {
        throw new Error(errorMessage.MONEY.DIVISIBLE);
      }
    },
  };

  export default Validate;
  ```

## <mark class="pink">2023.11.06(월)</mark>

### 🎯 2. 구매 개수와 랜덤 로또 번호 출력

- **controller > Controller.js**

  <div class="blue-box">
    <p>
      <div>quantity를 컨트롤러에서 계산해준 후 OutputView에 넘긴다.</div>
    </p>
  </div>

  ```jsx
  async start() {
    this.#money = await InputView.money();
    this.validate.money(this.#money);

    //변경사항
    this.#quantity = this.#money / constant.THOUSAND;
    OutputView.quantity(this.#quantity);
    this.#randomLottos = this.randomLotto.createRandom(this.#quantity);
    OutputView.randomLottos(this.#randomLottos);
  }
  ```

- **models > RandomLotto.js**

  ```jsx
  import { Random } from "@woowacourse/mission-utils";
  import constant from "../constants/constant.js";

  export default class RandomLotto {
    createRandom(quantity) {
      const randomLottos = Array.from({ length: quantity }, () =>
        Random.pickUniqueNumbersInRange(
          constant.MINIMUM,
          constant.MAXIMUM,
          constant.COUNT
        )
      );
      const sortedLottos = randomLottos.map((lotto) =>
        lotto.sort((a, b) => a - b)
      );
      return sortedLottos;
    }
  }
  ```

- **views > OutputView.js**

  ```jsx
  import { Console } from "@woowacourse/mission-utils";
  import gameMessage from "../constants/gameMessages.js";

  const OutputView = {
    quantity(quantity) {
      Console.print(gameMessage.OUTPUT.QUANTITY(quantity));
    },

    randomLottos(randomLottos) {
      randomLottos.forEach((randomLotto) => {
        Console.print(randomLotto);
      });
    },
  };

  export default OutputView;
  ```

### 🎯 1-1. 구입 금액 입력 수정

<div class="blue-box">
  <p>
    <div>문제 중에</div>
    <div>사용자가 잘못된 값을 입력할 경우 `throw`문을 사용해 예외를 발생시킨다. 그런 다음, "[ERROR]"로 시작하는 에러 메시지를 출력하고 해당 부분부터 입력을 다시 받는다.</div>
  </p>
  <p>→ 라는 부분이 달라졌는데 문제를 제대로 읽고 넘어가지 않았었다.</p>
</div>

- **views > InputView.js**
  <div class="blue-box">
    <p>
      <div> Validate 함수는 그대로 이고, InputView.money() 함수에서 예외가 발생하면 예외 문구를 출력하고 다시 입력 함수를 호출하여 재귀적으로 진행한다.</div>
    </p>
  </div>

  ```jsx
  import { Console } from "@woowacourse/mission-utils";
  import Validate from "../util/Validate.js";
  import gameMessage from "../constants/gameMessages.js";

  //변경사항
  const InputView = {
    async money() {
      const inputMoney = await Console.readLineAsync(gameMessage.INPUT.MONEY);
      try {
        Validate.money(inputMoney);
      } catch (error) {
        Console.print(error.message);
        return InputView.money();
      }
      return inputMoney;
    },

    async winningNumbers() {
      const inputWinning = await Console.readLineAsync(
        gameMessage.INPUT.WINNING
      );
      Validate.notNull(inputWinning);
      return inputWinning;
    },
  };

  export default InputView;
  ```

## <mark class="pink">2023.11.07(화)</mark>

### 💣 구조 대공사 - mvc 패턴 없애기

<div class="blue-box">
  <p>
    <div>잘못 입력했을 때 재입력 받으려고 하는데 입력은 view이고 유효성 검사는 model이다. 하지만 mvc 패턴은 무조건 controller에서 관리해야 하므로 이 부분을 해결하지 못 하여 컴포넌트 구조로 바꾸었다.</div>
  </p>
</div>

**폴더 구조**

```
src/
├── components/
│   ├── LottoGame.js
│   ├── InputMoney.js
│   ├── RandomLotto.js
│   ├── InputLotto.js
│   ├── Lotto.js
│   └── Result.js
├── constants/
│   ├── constant.js
│   ├── gameMessages.js
│   └── errorMessage.js
├── util/
│   └── Validate.js
├── App.js
└── index.js
```

**components 폴더**

- **LottoGame.js**

  ```jsx
  import { Console } from "@woowacourse/mission-utils";
  import constant from "../constants/constant.js";
  import gameMessage from "../constants/gameMessages.js";
  import InputMoney from "./InputMoney.js";
  import RandomLotto from "./RandomLotto.js";
  import InputLotto from "./InputLotto.js";
  import Lotto from "./Lotto.js";
  import Result from "./Result.js";

  export default class LottoGame {
    #money;

    #quantity;

    #winningNumbers;

    #bonusNumber;

    constructor() {
      this.randomLottos = [];
      this.result = {
        3: { match: "3개 일치", reward: "5,000", count: 0 },
        4: { match: "4개 일치", reward: "50,000", count: 0 },
        5: { match: "5개 일치", reward: "1,500,000", count: 0 },
        "5B": {
          match: "5개 일치, 보너스 볼 일치",
          reward: "30,000,000",
          count: 0,
        },
        6: { match: "6개 일치", reward: "2,000,000,000", count: 0 },
      };
    }

    async start() {
      await this.inputMoney();
      await this.outputRandom();
      await this.inputLottos();
      await this.lotto();
      await this.winningResult();
    }

    async inputMoney() {
      this.#money = await new InputMoney().input();
    }

    async outputRandom() {
      this.#quantity = this.#money / constant.THOUSAND;
      Console.print(gameMessage.OUTPUT.QUANTITY(this.#quantity));
      this.randomLottos = new RandomLotto().createRandom(this.#quantity);
      this.randomLottos.forEach((randomLotto) => {
        Console.print(`[${randomLotto.join(", ")}]`);
      });
    }

    async inputLottos() {
      const inputLotto = new InputLotto();
      this.#winningNumbers = await inputLotto.inputWinningNumbers();
      this.#bonusNumber = await inputLotto.inputBonusNumber(
        this.#winningNumbers
      );
    }

    async lotto() {
      Console.print(gameMessage.OUTPUT.RESULT_TEXT);
      this.randomLottos.forEach((randomLotto) => {
        const lotto = new Lotto(randomLotto);
        const matchCount = lotto.matchCount(this.#winningNumbers);
        const isBonusMatch = lotto.contains(this.#bonusNumber);
        this.result = lotto.count(this.result, matchCount, isBonusMatch);
      });
    }

    async winningResult() {
      new Result().output(this.result, this.#money);
    }
  }
  ```

- **InputMoney.js**

  ```jsx
  import { Console } from "@woowacourse/mission-utils";
  import Validate from "../util/Validate.js";
  import gameMessage from "../constants/gameMessages.js";

  export default class InputMoney {
    async input() {
      const inputMoney = await Console.readLineAsync(gameMessage.INPUT.MONEY);
      try {
        Validate.money(inputMoney);
      } catch (error) {
        Console.print(error.message);
        return this.input();
      }
      return inputMoney;
    }
  }
  ```

- **RandomLotto.js**

  ```jsx
  import { Random } from "@woowacourse/mission-utils";
  import constant from "../constants/constant.js";

  export default class RandomLotto {
    createRandom(quantity) {
      const randomLottos = Array.from({ length: quantity }, () =>
        Random.pickUniqueNumbersInRange(
          constant.MINIMUM,
          constant.MAXIMUM,
          constant.COUNT
        )
      );
      const sortedLottos = randomLottos.map((lotto) =>
        lotto.sort((a, b) => a - b)
      );
      return sortedLottos;
    }
  }
  ```

- **InputLotto.js**

  ```jsx
  import { Console } from "@woowacourse/mission-utils";
  import Validate from "../util/Validate.js";
  import gameMessage from "../constants/gameMessages.js";

  export default class InputLotto {
    async inputWinningNumbers() {
      const inputWinning = await Console.readLineAsync(
        gameMessage.INPUT.WINNING
      );
      try {
        Validate.winningNumbers(inputWinning);
      } catch (error) {
        Console.print(error.message);
        return this.inputWinningNumbers();
      }
      return inputWinning.split(",").map((winning) => Number(winning));
    }

    async inputBonusNumber(winningNumbers) {
      const inputBonus = await Console.readLineAsync(gameMessage.INPUT.BONUS);
      try {
        Validate.bonusNumber(inputBonus);
        Validate.notDuplicateBonus(winningNumbers, inputBonus);
      } catch (error) {
        Console.print(error.message);
        return this.inputBonusNumber(winningNumbers);
      }
      return Number(inputBonus);
    }
  }
  ```

- **Lotto.js**

  ```jsx
  export default class Lotto {
    #numbers;

    constructor(numbers) {
      this.#numbers = numbers;
    }

    matchCount(other) {
      const count = other.filter((number) => this.contains(number)).length;
      return count;
    }

    contains(number) {
      return this.#numbers.includes(number);
    }

    count(result, matchCount, isBonusMatch) {
      if (matchCount >= 3) {
        const key = matchCount === 5 && isBonusMatch ? "5B" : matchCount;
        result[key].count += 1;
      }
      return result;
    }
  }
  ```

- **Result.js**

  ```jsx
  import { Console } from "@woowacourse/mission-utils";
  import gameMessage from "../constants/gameMessages";

  export default class Result {
    output(result, money) {
      let income = 0;
      Object.entries(result).forEach(([key, value]) => {
        income += parseInt(value.reward.replace(/,/g, ""), 10) * value.count;
        Console.print(
          gameMessage.OUTPUT.RESULT(value.match, value.reward, value.count)
        );
      });
      const rate = ((income / money) * 100).toFixed(1);
      Console.print(gameMessage.OUTPUT.INCOME(rate));
    }
  }
  ```

## <mark class="pink">2023.11.08(수)</mark>

### ✅ 테스트

- **InputMoney.js**

  ```jsx
  import InputMoney from "../src/components/InputMoney";
  import { Console } from "@woowacourse/mission-utils";

  describe("InputMoney 클래스 테스트", () => {
    let inputMoney;

    beforeEach(() => {
      inputMoney = new InputMoney();
    });

    test("유효한 구매 금액 입력", async () => {
      const mockConsoleReadLineAsync = jest.fn(() => Promise.resolve("8000"));
      jest
        .spyOn(Console, "readLineAsync")
        .mockImplementation(mockConsoleReadLineAsync);

      const money = await inputMoney.input();

      expect(money).toEqual("8000");
      expect(mockConsoleReadLineAsync).toHaveBeenCalled();
    });

    test("유효하지 않은 구매 금액 입력 후 재시도", async () => {
      const mockConsoleReadLineAsync = jest
        .fn()
        .mockImplementationOnce(() => Promise.resolve(""))
        .mockImplementationOnce(() => Promise.resolve("a"))
        .mockImplementationOnce(() => Promise.resolve("20"))
        .mockImplementationOnce(() => Promise.resolve("8,000"))
        .mockImplementationOnce(() => Promise.resolve("8000"));
      jest
        .spyOn(Console, "readLineAsync")
        .mockImplementation(mockConsoleReadLineAsync);

      const money = await inputMoney.input();

      expect(money).toEqual("8000");
      expect(mockConsoleReadLineAsync).toHaveBeenCalledTimes(5);
    });
  });
  ```

- **InputLottoTest.js**

  ```jsx
  import InputLotto from "../src/components/InputLotto";
  import { Console } from "@woowacourse/mission-utils";

  describe("InputLotto 클래스 테스트", () => {
    let inputLotto;

    beforeEach(() => {
      inputLotto = new InputLotto();
    });

    test("유효한 당첨 번호 입력", async () => {
      const mockConsoleReadLineAsync = jest.fn(() =>
        Promise.resolve("1,2,3,4,5,6")
      );
      jest
        .spyOn(Console, "readLineAsync")
        .mockImplementation(mockConsoleReadLineAsync);

      const winningNumbers = await inputLotto.inputWinningNumbers();

      expect(winningNumbers).toEqual([1, 2, 3, 4, 5, 6]);
      expect(mockConsoleReadLineAsync).toHaveBeenCalled();
    });

    test("유효하지 않은 당첨 번호 입력 후 재시도", async () => {
      const mockConsoleReadLineAsync = jest
        .fn()
        .mockImplementationOnce(() => Promise.resolve(""))
        .mockImplementationOnce(() => Promise.resolve("1, 2,3,4, 5,6"))
        .mockImplementationOnce(() => Promise.resolve(",1,2,3,,4,5,6,"))
        .mockImplementationOnce(() => Promise.resolve("1,2,3,4"))
        .mockImplementationOnce(() => Promise.resolve("1,2,3,4,5,46"))
        .mockImplementationOnce(() => Promise.resolve("1,2,3,4,5,5"))
        .mockImplementationOnce(() => Promise.resolve("1,2,3,4,5,6"));
      jest
        .spyOn(Console, "readLineAsync")
        .mockImplementation(mockConsoleReadLineAsync);

      const winningNumbers = await inputLotto.inputWinningNumbers();

      expect(winningNumbers).toEqual([1, 2, 3, 4, 5, 6]);
      expect(mockConsoleReadLineAsync).toHaveBeenCalledTimes(7);
    });

    test("유효한 보너스 번호 입력", async () => {
      const mockConsoleReadLineAsync = jest.fn(() => Promise.resolve("7"));
      jest
        .spyOn(Console, "readLineAsync")
        .mockImplementation(mockConsoleReadLineAsync);

      const bonusNumber = await inputLotto.inputBonusNumber([1, 2, 3, 4, 5, 6]);

      expect(bonusNumber).toBe(7);
      expect(mockConsoleReadLineAsync).toHaveBeenCalled();
    });

    test("유효하지 않은 보너스 번호 입력 후 재시도", async () => {
      const mockConsoleReadLineAsync = jest
        .fn()
        .mockImplementationOnce(() => Promise.resolve(""))
        .mockImplementationOnce(() => Promise.resolve("a"))
        .mockImplementationOnce(() => Promise.resolve("46"))
        .mockImplementationOnce(() => Promise.resolve("6"))
        .mockImplementationOnce(() => Promise.resolve("7"));
      jest
        .spyOn(Console, "readLineAsync")
        .mockImplementation(mockConsoleReadLineAsync);

      const bonusNumber = await inputLotto.inputBonusNumber([1, 2, 3, 4, 5, 6]);

      expect(bonusNumber).toBe(7);
      expect(mockConsoleReadLineAsync).toHaveBeenCalledTimes(5);
    });
  });
  ```

- **LottoTest.js**

  ```jsx
  import Lotto from "../src/components/Lotto";

  describe("로또 클래스 테스트", () => {
    test("로또 번호가 주어진 다른 번호 배열과 0개 일치", () => {
      const lotto = new Lotto([1, 2, 3, 4, 5, 6]);
      const otherNumbers = [7, 8, 9, 10, 11, 12];
      const count = lotto.matchCount(otherNumbers);
      expect(count).toBe(0);
    });

    test("로또 번호가 주어진 다른 번호 배열과 3개 일치", () => {
      const lotto = new Lotto([1, 2, 3, 4, 5, 6]);
      const otherNumbers = [4, 5, 6, 7, 8, 9];
      const count = lotto.matchCount(otherNumbers);
      expect(count).toBe(3);
    });

    test("로또 번호가 주어진 다른 번호 배열과 6개 일치", () => {
      const lotto = new Lotto([1, 2, 3, 4, 5, 6]);
      const otherNumbers = [1, 2, 3, 4, 5, 6];
      const count = lotto.matchCount(otherNumbers);
      expect(count).toBe(6);
    });

    test("로또 번호가 주어진 숫자를 포함하는 경우", () => {
      const lotto = new Lotto([1, 2, 3, 4, 5, 6]);
      const number = 4;
      const contains = lotto.contains(number);
      expect(contains).toBe(true);
    });

    test("로또 번호가 주어진 숫자를 포함하지 않는 경우", () => {
      const lotto = new Lotto([1, 2, 3, 4, 5, 6]);
      const number = 7;
      const contains = lotto.contains(number);
      expect(contains).toBe(false);
    });
  });
  ```

- **ResultTest.js**

  ```jsx
  import Result from "../src/components/Result";
  import { MissionUtils } from "@woowacourse/mission-utils";

  const getLogSpy = () => {
    const logSpy = jest.spyOn(MissionUtils.Console, "print");
    logSpy.mockClear();
    return logSpy;
  };

  describe("Result 클래스 테스트", () => {
    let result;

    beforeEach(() => {
      result = new Result();
    });

    test("output 메서드 테스트", () => {
      const logSpy = getLogSpy();

      const sampleResult = {
        3: { match: "3개 일치", reward: "5,000", count: 1 },
        4: { match: "4개 일치", reward: "50,000", count: 2 },
        5: { match: "5개 일치", reward: "1,500,000", count: 0 },
        "5B": {
          match: "5개 일치, 보너스 볼 일치",
          reward: "30,000,000",
          count: 0,
        },
        6: { match: "6개 일치", reward: "2,000,000,000", count: 0 },
      };
      const sampleMoney = 100000;

      result.output(sampleResult, sampleMoney);

      const logs = [
        "3개 일치 (5,000원) - 1개",
        "4개 일치 (50,000원) - 2개",
        "5개 일치 (1,500,000원) - 0개",
        "5개 일치, 보너스 볼 일치 (30,000,000원) - 0개",
        "6개 일치 (2,000,000,000원) - 0개",
        "총 수익률은 105.0%입니다.",
      ];

      logs.forEach((log) => {
        expect(logSpy).toHaveBeenCalledWith(expect.stringContaining(log));
      });
    });
  });
  ```

**과제 소감문**

입력 받을 것이 세 개나 되고, Lotto.js 파일을 어떻게 활용해야 하는 지 감이 잡히지 않아 초반에 많은 시행착오를 겪었다. 우테코 저번 기수 글을 보니 4주차는 MVC 패턴으로 짜게 되어있었다는 후기를 보고 이번 주차에 미리 연습을 하려고 MVC 패턴으로 코드를 짜고 있었다.

Lotto.js를 모델로 두고 여기서 유효성 검사를 하려고 했는데 이번 과제는 저번 과제와 달리 예외 문구를 출력한 후 그 부분부터 다시 입력을 받아야 했다. 입력은 view에서 하고 유효성 검사는 model에서 하고 재입력을 받을 것인지 관리하는 것은 controller에서 하는게 효율 적이지 못하다고 생각했고 월요일에 mvc 패턴에서 컴포넌트 구조로 갈아 엎었다. 내 3일이 부정 당하는 것 같아 갈아엎기까지 고민을 많이 했지만 결론은 컴포넌트 구조로 한 게 이번 과제에 더 적합하다고 생각했다.

그 이유는 도메인 별로 테스트를 작성해야 하는데 mvc 패턴은 도메인이 많이 쪼개져 있어서 테스트가 쉽지 않았을 것 같기 때문이다. 또 클래스(객체)를 분리하는 연습을 하려면 접근 제어자를 적절하게 사용해야 하는데 mvc 패턴은 값을 계속 넘겨 주어야 해서 접근 제어자를 거의 사용하지 못 하기 때문이다.

바닐라 js 코드를 짜본 적이 없는데 문제를 보고 클래스와 함수를 빠르게 생각할 수 있는 데까지 3주가 걸렸다. 아직도 부족한 게 많지만 나머지 한 주에 지금까지 미뤘던 공부들을 더 해서 좀 더 발전된 코드를 짜려고 한다.
