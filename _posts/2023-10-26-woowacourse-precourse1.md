---
layout: single
title: "[우테코]프리코스 1주차"
categories: woowacourse
toc: true
toc_sticky: true
---

# ⚾ 숫자 야구 게임

<mark style='color: red'>※ 진블로그는 링크가 있는 사람만 조회할 수 있어서 저만의 기록 공간으로 활용하고 있습니다.</mark>

## <mark class="pink">2023.10.18(수)</mark>

디스코드에 초대됐다.

디스코드의 `잡담` 채널에서 많은 인사와 잡담이 나눠졌다.

`함께-나누기` 에서는 윗 기수분들의 꿀팁이나 현재 기수분들의 블로그 정리 글등의 올라왔다. (다들 정리 너무 잘하신다... 감사합니다)

또 특이한게 공지사항 같은 데엔 보통 로봇이 공지글 쓴 것 마냥 딱딱하거나 감정을 빼놓은 글이 많은데 여긴 아니다. 멘토분들이 따뜻하게 공지글 남겨주시고 귀여운 임티도 있었당🤗 아래는 우테코를 임하는 마인드에 대해 나와있는 링크이다.

경쟁자랑 함께 성장하라고요?  
👉🏻 [https://story.baemin.com/6193](https://story.baemin.com/6193/)

---

## <mark class="pink">2023.10.19(목)</mark>

3시에 과제가 메일로 왔다. 이미 10분 전부터 디스코드는 뜨거웠다. 내 메일은 3시 13분에 왔다. 지원자가 많아서 그런 것 같은데 디스코드에 초대된 인원만 봐도 4555명이다. 오늘은 4시부터 11시 알바니까 내일부터 해야지

바닐라 js로 코드 짜본 적이 한번도 없어서 급하게 딥다이브 책을 샀다. 이거 공부하면서 우테코랑 병행해야겠다. 이론 공부와 실습을 같이 할 수 있어서 좋다.

---

## <mark class="pink">2023.10.20(금)</mark>

### 🔥 설치하기

앞으로는 작성되지 않을.. 1주차니까 코린이답게 설치하는 방법도 적어두려고 한다.

1주차 미션 깃허브  
👉🏻 [https://github.com/woowacourse-precourse/javascript-baseball-6](https://github.com/woowacourse-precourse/javascript-baseball-6)

제출 참고 사이트  
👉🏻 [프리코스 과제 제출](https://github.com/woowacourse/woowacourse-docs/tree/master/precourse), [우아한테크코스 지원 사이트](https://apply.techcourse.co.kr/), [제출 가이드](https://github.com/woowacourse/woowacourse-docs/tree/master/precourse#%EC%A0%9C%EC%B6%9C-%EA%B0%80%EC%9D%B4%EB%93%9C)

---

README.md를 그대로 따라하면 된다.

1\. fork 하기  
2\. `git clone https://github.com/chlwlstlf/javascript-baseball-6.git`  
3\. `npm i`

<details>
<summary>⚠ node 버전이 안 맞아서 오류가 떴다</summary>
<div markdown="1">
오류 메세지

```
C:\Users\LG\Desktop\취업\15_우테코\javascript-baseball-6>npm install
npm WARN config global `--global`, `--local` are deprecated. Use `--location=global` instead.
npm ERR! code EBADENGINE
npm ERR! engine Unsupported engine
npm ERR! engine Not compatible with your version of node/npm: javascript-baseball@1.0.0
npm ERR! notsup Not compatible with your version of node/npm: javascript-baseball@1.0.0
npm ERR! notsup Required: {"npm":">=9.6.7","node":">=18.17.1"}
npm ERR! notsup Actual:   {"npm":"9.2.0"}

npm ERR! A complete log of this run can be found in:
npm ERR!     C:\Users\LG\AppData\Local\npm-cache\_logs\2023-10-20T04_45_17_214Z-debug-0.log
```

---

해결 과정

1️⃣ **Node.js와 npm 버전 확인**

Node.js 버전 16.16.0 → 18.17.1 이상으로 바꾸기

```
C:\Users\LG\Desktop\취업\15_우테코\javascript-baseball-6>node -v
v16.16.0
```

npm 버전 9.2.0 → 9.6.7 이상으로 바꾸기

```
C:\Users\LG\Desktop\취업\15_우테코\javascript-baseball-6>npm -v
npm WARN config global `--global`, `--local` are deprecated. Use `--location=global` instead.
9.2.0
```

---

2️⃣ **Node.js와 npm 업데이트**

Node.js 업데이트: 18.18.2 다운로드 받기  
👉🏻 [https://nodejs.org/en/download](https://nodejs.org/en/download)

![Untitled (4)](https://github.com/chlwlstlf/data/assets/63334368/4372c3a0-edc5-4acd-ab6e-5f2bbee830e7)

![Untitled (5)](https://github.com/chlwlstlf/data/assets/63334368/6018b6a5-28c6-4a12-856e-d0be6cb9dbc7)

npm 업데이트

```bash
npm uninstall -g npm
npm install -g npm@9.6.7
```

해결!

```bash
C:\Users\LG\Desktop\취업\15_우테코\javascript-baseball-6>npm i

added 380 packages, and audited 381 packages in 4s

32 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```

  </div>
</details>

4\. `npm test`

---

### 🚀 기능 요구사항

<mark style='background-color: #fff5b1'>숫자야구 - 1부터 9까지 서로 다른 수로 이루어진 3자리의 수를 맞추는 게임이다.</mark>

1\. 게임 시작 문구 출력  
 `숫자 야구 게임을 시작합니다.`

2\. 입력1 - 서로 다른 3자리의 수  
 `숫자를 입력해주세요 : 123`

3\. 출력1 - 입력한 수에 대한 결과를 볼, 스트라이크 개수로 표시

- 입력한 수에 대한 결과를 볼, 스트라이크 개수로 표시  
  `1볼 1스트라이크`
- 하나도 없는 경우  
  `낫싱`
- 3개의 숫자를 모두 맞힐 경우  
  `3스트라이크`  
  `3개의 숫자를 모두 맞히셨습니다! 게임 종료`

4\. 입력2 - 게임이 끝난 경우 재시작/종료를 구분하는 1과 2 중 하나의 수  
 `게임을 새로 시작하려면 1, 종료하려면 2를 입력하세요.`  
 `1`

5\. 예외처리 - 에러 문구 출력 `[ERROR]` 로 시작해야 한다.

---

### 🎯 프로그래밍 요구사항

이전 기수들의 프로그래밍 요구사항을 보니까 조건이 많이 사라진 것 같다. 1주차라 그런가..?

1\. 순수 Vanilla JS로만 구현

2\. JavaScript 코드 컨벤션을 지키기

- 우테코 JS 코드 컨벤션  
  👉🏻 [https://github.com/woowacourse/woowacourse-docs/tree/main/styleguide/javascript](https://github.com/woowacourse/woowacourse-docs/tree/main/styleguide/javascript)
- Airbnb JS 코드 컨벤션  
  👉🏻 [https://github.com/airbnb/javascript](https://github.com/airbnb/javascript)

<details>
<summary>🔔 JavaScript 코드 컨벤션이란?</summary>
<div markdown="1">

- 영어 사용, SNAKE_CASE 사용
- `var`보단 `const`와 `let`을 사용하자!
- `new Object()` 보단 `{}` 를 사용하자!
  `new Array()` 보단 `[]` 를 사용하자!
- 얕은 복사 보단 스프레드 연산자 사용하자!

  ```jsx
  //object
  const original = { a: 1, b: 2 };
  const copy = { ...original, c: 3 };

  //array
  const itemsCopy = [...items];
  ```

- 배열에 요소를 추가할 때는 `push` 를 사용하자!

  ```jsx
  someStack.push("abracadabra");
  ```

- 배열로 만들고 싶으면 `Array.from` 을 사용하자!

  ```jsx
  const arr = Array.from(arrLike);
  ```

- 구조 분해를 사용하자!

  ```jsx
  //object
  function getFullName({ firstName, lastName }) {
    return `${firstName} ${lastName}`;
  }

  //array
  const [first, second] = arr;
  ```

- 문자열은 `''` 사용하자!
- 문자열 연결할 때는 탬플릿 리터럴(` `)을 사용하자!

  ```jsx
  function sayHi(name) {
    return `How are you, ${name}?`;
  }
  ```

- 함수는 선언하지 말고 할당하자!

  ```jsx
  let test;
  if (currentUser) {
    test = () => {
      console.log("Yup.");
    };
  }
  ```

</div>
</details>

---

### 📝 공부한 것

**MVC 패턴**

- Model : 데이터를 다루는 부분. 비즈니스 로직을 포함한다.
- View : 레이아웃과 화면을 다루는 부분.
- Controller : 사용자의 명령을 받아 Model과 View 부분을 이어주는 부분.

→ 이번 과제를 MVC 패턴으로 짜는 게 의미가 있을까? View를 안 만들어도 되는데..?

## <mark class="pink">2023.10.21(토)</mark>

시작도 못 하고 있었기 때문에.. 디스코드 `함께 나누기`를 들어가봤다. 오늘 2시에 프론트 분께서 js에 대해 자세히 설명한 링크를 올려주셨다😀 이 글을 보면서 공부한 내용을 적어두려고 한다.

👉🏻 [프론트를 위한 JS class 원리 및 사용법](https://github.com/zxcev/zapddongsani/blob/main/front_oop1.md)

---

### 📝 JS 객체 생성

**1-1. 원래 문법(숨겨진 문법)**

- 첫 번째 인자로 this가 숨겨져있다. this는 새로 생성한 student 객체
- 따라서 반환값 없이 student의 빈 객체에 필드를 넣어주는 것

  ```jsx
  function Student(this, name, age) {
    this.name = name;
    this.age = age;
  }

  const student = new Student(name, age);
  const student = {};
  Student(student, name, age);
  ```

---

**1-2. class를 사용한 객체 생성**

- 클래스명이 결국 함수명이 되고, 생성자의 인자와 내부 로직이 그대로 함수 인자 및 바디가 된다.
- `new`를 붙인 함수 호출은 빈 객체를 생성하고 첫 번째 인자로 넘겨서 필드를 채워 넣는다.
- `new`를 붙이지 않으면 알고 있으신대로 일반적인 함수 호출이 된다.

  ```jsx
  //class
  class Person1 {
    constructor(name, age) {
      this.name = name;
      this.age = age;
    }
  }
  const person1 = new Person1("abc", 1);

  //function
  function Person2(name, age) {
    this.name = name;
    this.age = age;
  }
  const person2 = new Person2("xxx", 2);
  ```

---

**2-1. 객체에 동작 추가하기**

- 메소드: 객체 안에 있는 함수, 인자로 전달하지 않아도 된다, ‘주소’를 담고 있다

  ```jsx
  class Person {
    constructor(name, age) {
      this.name = name;
      this.age = age;
    }
    eat() {
      console.log(this.name + "is eating");
    }
  }

  const person = new Person("abc", 1);
  person.eat();
  ```

---

**2-2. 객체의 유효성 검사**

- 모든 객체는 `__proto__`라는 속성을 갖고 있다.
- `new Person`으로 생성했기 때문에 `a.__proto__`의 `constructor`는 `class Person`이라 적혀 있다.
- 유효성 검사를 위한 로직 분리

  ```jsx
  class Person {
    constructor(name, age, height) {
      // 유효성 검사 함수 호출
      this.validate(name, age, height);
      this.name = name;
      this.age = age;
      this.height = height;
    }

    // 유효성 검사를 위한 로직 분리
    validate(name, age, height) {
      if (age < 0) {
        throw new Error("나이는 반드시 양수가 되어야 합니다.");
      }
      if (height < 0) {
        throw new Error("키는 반드시 양수가 되어야 합니다.");
      }

      if (name !== typeof "string" && name.length < 1) {
        throw new Error("유효하지 않은 이름입니다.");
      }
    }
  }
  ```

---

**3-1. class와 proto 파헤치기**

- `class`는 생성자와 메소드를 가진다.
- `new <Class명>`으로 호출 시, `constructor`가 호출된다. (`Person class`라면 `Person.constructor` 호출)
- `__proto__`라는, `constructor` 및 해당 클래스의 메소드를 모두 가진 객체를 포함한 빈 객체를 생성하여 첫 번째 인자로 전달한다.
- 그 빈 객체에 필드(속성)를 하나하나 넣어준다.

  ```jsx
  // 1. 원래 문법
  const person = new Person('a', 1);

  // 2. 실제 일어나는 과정 및 결과
  const person = {
    __proto__: {
      constructor: /* Person의 constructor */,
      eat: /* Person의 메소드 */,
      sleep: /* Person의 메소드 */,
      walk: /* Person의 메소드 */,
    },
  };

  // Person의 constructor를 호출하여 위에서 생성한 객체에 필드를 추가
  // 첫 번째로 들어가는 person이 바로 this
  Person.constructor(person, 'a', 1);

  // 생성된 최종 person 객체
  {
    name: 'a',
    age: 1,
    __proto__: {
      constructor: /* Person의 constructor */,
      eat: /* Person의 메소드 */,
      sleep: /* Person의 메소드 */,
      walk: /* Person의 메소드 */,
    },
  }
  ```

---

**3-1. 접근 제어자**

- 객체는 생성부터 소멸 시점까지 유효한 데이터를 가져야 한다.
- 즉, 값을 변경할 때도 유효성 검사를 해야한다는 뜻이다.
- `#`(private access modifier)를 붙이면 private 필드가 된다.

  ```jsx
  class Person {
    #name;
    #age;
    constructor(name, age) {
      this.validate(name, age);
      this.#name = name;
      this.#age = age;
    }
    validate(name, age, height) {
      if (age < 0) {
        throw new Error("나이는 반드시 양수가 되어야 합니다.");
      }
      if (name !== typeof "string" && name.length < 1) {
        throw new Error("유효하지 않은 이름입니다.");
      }
    }
    getName() {
      return this.#name;
    }
    getAge() {
      return this.#age;
    }
    setName(name) {
      // name을 변경할 때마다 유효한 데이터인지 검증
      validateName(name);
      this.#name = name;
    }
    setAge(age) {
      // age를 변경할 때마다 유효한 데이터인지 검증
      validateAge(age);
      this.#age = age;
    }
  }

  const person = new Person("abc", 1);
  person.setAge(-1); //유효성 검사 가능
  ```

---

### 📝 자주 쓰이는 JS 내장 함수

**1\. map**

map()은 배열 내의 모든 요소 각각에 대하여 주어진 함수(콜백)를 호출한 결과를 모아 새로운 배열을 반환

`.map(x => x에 대해 arrow function);.`  
`.map(function(x) { x에 대한 function});`

```jsx
const arr = [1, 2, 3, 4];
const mulArr = arr.map((num) => num * 3);
console.log(mulArr); //[3, 6, 9, 12]
```

---

**2\. forEach**

forEach()가 배열 요소마다 한 번씩 주어진 함수(콜백)를 실행

```jsx
const arr = [1, 2, 3, 4];
const mulArr = [];
arr.forEach((num) => {
  mulArr.push(num * 3);
});
console.log(mulArr); //[3, 6, 9, 12]
```

---

**3\. reduce**

배열의 각 요소에 대해 주어진 리듀서(reducer) 함수를 실행하고, 하나의 결과값을 반환한다.

```jsx
arr.reduce(callback[, initialValue])
```

- callback : 배열의 각 요소에 대해 실행할 함수. 다음 네 가지 인수를 가짐
  - accumulator : 누산기. 콜백의 반환값을 누적. 콜백의 이전 반환값 또는, 콜백의 첫 번째 호출이면서 initialValue를 제공한 경우에는 initialValue의 값임
  - currentValue : 처리할 현재 요소
  - currentIndex (Optional) : 처리할 현재 요소의 인덱스. initialValue를 제공한 경우 0, 아니면 1부터 시작
  - array (Optional) : reduce()를 호출한 배열
- initialValue (Optional) : callback의 최초 호출에서 첫 번째 인수에 제공하는 값. 초기값을 제공하지 않으면 배열의 첫 번째 요소를 사용. 빈 배열에서 초기값 없이 reduce()를 호출하면 오류 발생

```jsx
const numbers = [2, -5, -123, 59, -5480, 24, 0, -69, 349, 3];
const result = numbers.reduce(
  (acc, cur, idx) => {
    if (cur < 0) {
      acc[0]++;
    } else if (cur > 0) {
      acc[1]++;
    }
    return acc;
  },
  [0, 0]
);
console.log(result); // [4, 5]
```

---

**4\. every**

배열의 모든 원소가 조건에 맞는지 검사하는 메서드

```jsx
arr.every(callback(curentValue, index, array)[, thisArg])
```

- callback : 배열의 각 요소에 대해 실행할 함수. 다음 네 가지 인수를 가짐
  - curentValue: 현재 요소 (ex. 반복문의 현재 요소)
  - index : 현재 요소의 인덱스
  - array :대상 배열
- thisArg : callback을 실행할 때 this로 사용하는 값.

```jsx
var arr = [0, 1, 2, 3, 4];

// result : false
arr.every(function (elem, index, arr) {
  return elem < 4;
});

// result : true
arr.every((elem, index, arr) => elem < 5);
```

---

### 💻 개발 시작

**1\. 나의 브랜치 만들기**

PR 날렸을 때 나의 브랜치를 파악하기 위해 만들었다.

- `git branch chlwlstlf`
- `git checkout chlwlstlf`

---

**2\. 자바스크립트 실행 시키기**

- vscode에서 `code runner` 설치하기
- Ctrl+Alt+N을 누르면 실행된다.

---

**3\. airbnb config 설치**

airbnb 자바스크립트 스타일 가이드를 준수한다.

`npx install-peerdeps --dev eslint-config-airbnb`

→ package.json 파일이 변경되면 안 되는 조건이 있어서 다시 uninstall 했다.

---

**4\. 기능 명세서 작성하기**

```markdown
# 🎯 기능 명세서

1. 랜덤 3자리 수 만들기

- 1~9 숫자이고, 3개 모두 다른 숫자여야 한다.

2. 숫자 입력

- `Console.readLineAsync` 사용한다.
- 3자리 수가 아닐 때, 숫자가 아닐 때, 중복된 수가 있을 때 예외처리 해준다 → throw문으로 예외 발생 후 종료시킨다.
- 에러 문구는 “[ERROR]“로 시작해야 한다.

3. 볼과 스트라이크 개수

- `Console.print` 사용한다.
- 볼이 먼저 출력되어야 한다.
- 정답일 때, 3 스크라이크 출력 후 맞혔다는 문구를 출력한다.

4. 게임이 끝난 경우 재시작/종료를 구분하는 1과 2 중 하나의 수

- 1을 입력하면 게임을 다시 시작하고, 2를 입력하면 게임을 종료한다.
- 잘못된 숫자를 입력했을 때, 에러 문구는 “[ERROR]“로 시작해야 한다.
```

---

**5\. 폴더 구조**

```
src/
├── components/
│   ├── baseballGame.js
│   ├── createNumber.js
│   ├── compareNumber.js
│   └── vaildNumber.js
├── util/
│   ├── Constants.js
│   └── Messages.js
├── App.js
└── index.js
```

---

**6\. util 폴더**

- Constants.js

  ```jsx
  const Constants = Object.freeze({
    RANDOM_DIGIT: 3,
    MINIMUM: 1,
    MAXIMUM: 9,
    RESTART: 1,
    EXIT: 2,
  });

  export default Constants;
  ```

- Message.js

  ```jsx
  const Messages = Object.freeze({
    START: "숫자 야구 게임을 시작합니다.",
    INPUT_NUMBER: "숫자를 입력해주세요 : ",
    RESULT: "3개의 숫자를 모두 맞히셨습니다! 게임 종료",
    RESTART_OR_EXIT: "게임을 새로 시작하려면 1, 종료하려면 2를 입력하세요.\n",
    GAME_OVER: "숫자 야구 게임을 종료합니다.",

    ERROR: {
      REJECTED_READLINE: "[ERROR] readLineAsync Promise가 거부되었습니다.",
      INVALID_BALL_NUMBER: "[ERROR] 3자리 숫자를 중복없이 입력해주세요.",
      INVALID_SELECT_NUMBER: "[ERROR] 1 혹은 2만 입력해주세요.",
    },
  });

  export default Messages;
  ```

## <mark class="pink">2023.10.22(일)</mark>

### 🎯 1. 랜덤 3자리 수 만들기

**1\. @woowacourse/mission-utils 패키지 설치**

👉🏻 [MissionUtils 깃허브](https://github.com/woowacourse-projects/javascript-mission-utils#mission-utils)

`npm install @woowacourse/mission-utils`

→ package.json에 있기 때문에 npm i를 했다면 다시 설치할 필요는 없다.

---

**2\. 코드 짜기**

- CreateNumber.js

  ```jsx
  import { Random } from "@woowacourse/mission-utils";

  export class CreateNumber {
    constructor() {
      this.randomNumber = this.generateRandomNumbers();
    }

    generateRandomNumbers() {
      const numbers = [];
      while (numbers.length < 3) {
        const number = Random.pickNumberInRange(1, 9);
        if (!numbers.includes(number)) {
          numbers.push(number);
        }
      }
      return numbers;
    }
  }
  ```

- App.js

  ```jsx
  import { Console } from "@woowacourse/mission-utils";
  import { CreateNumber } from "./components/CreateNumber.js";

  class App {
    async play() {
      const createNumber = new CreateNumber();

      Console.print("숫자 야구 게임을 시작합니다.");
      Console.print(createNumber.randomNumber);
    }
  }
  export default App;
  ```

  출력 결과

  ```
  숫자 야구 게임을 시작합니다.
  [ 5, 2, 8 ]
  ```

---

### 🎯 2. 숫자 입력

**1\. 숫자 입력 받기**

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <b>🔔 vscode에서 입력 받기</b>
    <div>- Ctrl+Alt+N은 실행일 뿐 입력을 받을 수 없다. console만 확인할 수 있다.</div>
    <div>- src > index.js 파일을 실행시키고 싶으면 `cd src` , `node index.js` 를 차례대로 터미널에 입력하면 된다.</div>
  </p>
</div>

- mission-utils 문서

  ```jsx
  // readLineAsync(query)
  // 주어진 질문을 화면에 출력하고, 사용자가 입력한 답변을 Promise를 통해 반환한다.

  async function getUsername() {
    try {
      const username = await Console.readLineAsync("닉네임을 입력해주세요.");
    } catch (error) {
      // reject 되는 경우
    }
  }
  ```

- baseballGame.js 코드
  <div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
    <p>
      <div>- asynce getUserInput 함수를 부르고 await으로 받는다.</div>
      <div>- getUserInput이 비동기 함수이기 때문에 이를 감싸는 start 함수도 비동기여야 한다.</div>
    </p>
  </div>

  ```jsx
  import { Console } from "@woowacourse/mission-utils";
  import { CreateNumber } from "./createNumber.js";
  import Messages from "../util/Messages.js";

  class BaseballGame {
    async start() {
      this.randomNumber = new CreateNumber().randomNumber;
      while (true) {
        const userNumber = await this.getUserInput();
        break;
      }
    }
    async getUserInput() {
      try {
        const userNumber = await Console.readLineAsync(
          "숫자를 입력해주세요 : "
        );
        return userNumber;
      } catch (error) {
        throw new Error(Messages.ERROR.REJECTED_READLINE);
      }
    }
  }

  export default BaseballGame;
  ```

**2\. 유효성 검사하기**

- vaildateNumber.js 코드

  ```jsx
  export function vaildateNumber(number) {
    const numberSet = new Set(number);
    const numberRegExp = new RegExp(/[1-9]{3}/g);

    if (numberSet.size !== 3 || !numberRegExp.test(number)) {
      return false;
    }
    return true;
  }
  ```

- baseballGame.js 코드

  ```jsx
  import { Console } from "@woowacourse/mission-utils";
  import { CreateNumber } from "./createNumber.js";
  import { vaildateNumber } from "./vaildateNumber.js";
  import Messages from "../util/Messages.js";

  class BaseballGame {
    async start() {
      const randomNumber = new CreateNumber().randomNumber;
      while (true) {
        const userNumber = await this.getUserInput();
        // 유효성 검사
        if (!vaildateNumber(userNumber)) {
          throw new Error(Messages.ERROR.INVALID_BALL_NUMBER);
        }
      }
    }
    async getUserInput() {
      try {
        const userNumber = await Console.readLineAsync(
          "숫자를 입력해주세요 : "
        );
        return userNumber;
      } catch (error) {
        throw new Error(Messages.ERROR.REJECTED_READLINE);
      }
    }
  }

  export default BaseballGame;
  ```

---

### 🎯 3. 볼과 스트라이크 개수

**1\. 볼, 스트라이크 출력하기**

- compareNumber.js

  ```jsx
  import { Console } from "@woowacourse/mission-utils";
  import Constants from "../util/Constants.js";

  export function compareNumber(target, guess) {
    let ball = 0;
    let strike = 0;
    // 개수 세기
    for (let i = 0; i < Constants.RANDOM_DIGIT; i++) {
      if (target[i] == guess[i]) {
        strike++;
      } else if (target.includes(guess[i])) {
        ball++;
      }
    }
    // 출력
    let resultString = "";
    if (ball) resultString += `${ball}볼 `;
    if (strike) resultString += `${strike}스트라이크`;
    if (!ball && !strike) resultString += "낫싱";
    Console.print(resultString);
    // 반환값
    if (strike == Constants.RANDOM_DIGIT) return true;
    else return false;
  }
  ```

- baseballGame.js

  ```jsx
  import { Console } from "@woowacourse/mission-utils";
  import { CreateNumber } from "./createNumber.js";
  import { vaildateNumber } from "./vaildateNumber.js";
  import { compareNumber } from "./compareNumber.js";
  import Messages from "../util/Messages.js";

  class BaseballGame {
    async start() {
      const randomNumber = new CreateNumber().randomNumber;
      while (true) {
        // 숫자 입력 받기
        const inputNumber = await this.getUserInput();

        // 유효성 검사
        if (!vaildateNumber(inputNumber)) {
          throw new Error(Messages.ERROR.INVALID_BALL_NUMBER);
        }

        // userNumber를 문자열에서 배열로 바꾸기
        const userNumber = inputNumber.split("").map(Number);

        // 볼, 스트라이크 검사
        if (compareNumber(randomNumber, userNumber)) {
          Console.print(Messages.RESULT);
          break;
        }
      }
    }

    async getUserInput() {
      try {
        const userNumber = await Console.readLineAsync(Messages.INPUT_NUMBER);
        return userNumber;
      } catch (error) {
        throw new Error(Messages.ERROR.REJECTED_READLINE);
      }
    }
  }

  export default BaseballGame;
  ```

### 🎯 4. 게임이 끝난 경우 재시작/종료를 구분하는 1과 2 중 하나의 수

**1\. 입력 받은 후 유효성 검사**

- App.js

  ```jsx
  import { Console } from "@woowacourse/mission-utils";
  import BaseballGame from "./components/baseballGame.js";
  import Messages from "./utils/Messages.js";
  import Constants from "./utils/Constants.js";

  class App {
    async play() {
      Console.print(Messages.START);
      while (true) {
        await new BaseballGame().start();
        const selectNumber = Number(await this.getExitInput());
        if (![Constants.RESTART, Constants.EXIT].includes(selectNumber)) {
          throw new Error(Messages.ERROR.INVALID_SELECT_NUMBER);
        }
        if (selectNumber === Constants.EXIT) {
          break;
        }
      }
    }

    async getExitInput() {
      try {
        const selectNumber = await Console.readLineAsync(
          Messages.RESTART_OR_EXIT
        );
        return selectNumber;
      } catch (error) {
        throw new Error(Messages.ERROR.REJECTED_READLINE);
      }
    }
  }
  export default App;
  ```

## <mark class="pink">2023.10.25(수)</mark>

**과제 제출 오류**

이 엄청 많은 Fix들…  
알고 봤더니 깃허브는 파일명을 지멋대로 바꾼다. 대문자, 소문자 구분을 안 하는 듯.  
그치만 내 코드에선 파일 명이 소문자로 되어있기 때문에 인식을 못 했던 거였다.  
다음주부턴 이런 실수를 하지 않아야 겠다.

![Untitled (7)](https://github.com/chlwlstlf/data/assets/63334368/ae597aee-290d-499a-a166-6bf778f5f12c)

![Untitled (6)](https://github.com/chlwlstlf/data/assets/63334368/00ae7ccb-1ee2-41ec-919e-dd28fbabdbdc)

**과제 진행 소감**

npm i부터 에러가 났고, vscode에서 자바스크립트 입력하는 법도 몰랐기 때문에 이런 부분에서 하루를 다 날린 적도 있다. 프론트 엔드 개발자로서 언젠가 해야할 일이었지만 미뤘던 과거의 나를 반성하며.. 열심히 해결하려고 하였다.

MVC 패턴, JS 객체 생성, map,forEach 등 다양한 내장 함수를 공부하느라고 일요일부터 코드 짜는 것을 시작하였지만 막상 코드에서는 JS 객체 생성 외에는 아무것도 사용하지 못 했다. View가 없었기 때문에 MVC 패턴을 짜는 데에 어려움이 있었고 1주차의 다른 사람들 코드를 참고하며 2주차부터 적용해도 늦지 않을 거라는 생각을 하였다.

상수와 메세지 관리를 하는 파일을 따로 만들었는데 다른 사람들도 이렇게 했을 지 궁금하다. 다음주엔 함수와 메소드, MVC 패턴 등을 더 공부하여 어떻게 class를 만들면 더 효율적일지 생각해보아야 겠다. 이번주 과제 중에 유효성 검사와, 볼스트라이크 출력하는 것을 함수로 만들었는데 객체 메소드로 만들까 고민을 많이 했었다. 굳이 객체일까 싶어 함수로 하였는데 다른 사람들 코드를 보며 참고해야겠다.

아직은 많이 스파게티 코드겠지만 2주차 부터는 정돈된 코드를 짜도록 노력할 것이다. 피어 리뷰를 열심히 당하고 싶다.. 피드백이 간절한 시기이다.
