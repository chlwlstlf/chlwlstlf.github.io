---
layout: single
title: "[우테코]Level1 컴포넌트, Typescript, E2E 테스트"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션3 LMS 강의 자료1

## <mark class="pink">📖컴포넌트</mark>

### <mark class="yellow">Top-down vs Bottom-up</mark>

앱을 구성할 때 하향식으로 구체화해나가거나, 상향식으로 필요한 블럭들을 먼저 만들고 조립해나가는 식으로 만들 수 있다. 어느 한 가지 방법만 사용한다기보다 필요에 따라 유연하게 전환해가며 구성한다.

**Top -> Down**  
[](https://techcourse-storage.s3.ap-northeast-2.amazonaws.com/84d748b65a904bb4b7f65f2b755fdf0f)

**Bottom -> Up**  
[](https://techcourse-storage.s3.ap-northeast-2.amazonaws.com/dbf0ba6cdcab4ae6aab092998c3466fa)

<br>
<br>

### <mark class="yellow">컴포넌트</mark>

- 명확한 하나의 역할을 하며
- 재사용하기 쉽고
- 조립해서 필요한 UI를 구성하기 쉽게 만들어 줄 수 있다.

<br>
<br>

## <mark class="pink">📖Typescript</mark>

### <mark class="yellow">주요 키워드</mark>

**옵셔널**

- `?` 키워드를 사용하면 매개변수의 타입이 자동으로 **타입|undefined**로 확장된다.

  ```ts
  function greet(name: string, age?: number) {}

  greet("Jinsil", 25);
  greet("Jinsil");
  ```

- 기본값을 설정할 수 있다.
  ```ts
  function greet(name: string, age: number = 25) {}
  ```
- 옵셔널 매개변수는 반드시 필수 매개변수 뒤에 위치해야 한다. 그렇지 않으면 컴파일 오류가 생긴다. 객체를 매개변수로 사용하면 순서와 관계없이 사용할 수 있다.

  ```ts
  // 객체 매개변수
  function createUser({
    name,
    age,
    email,
  }: {
    name: string;
    age?: number;
    email: string;
  }) {}
  ```

<br>

**Interface**

- 객체의 구조를 정의하거나 클래스의 타입을 선언할 때 사용한다.

  ```ts
  interface Person {
    name: string;
    age: number;
  }

  const user: Person = { name: "Bob", age: 30 };
  ```

- 확장이 가능 (extends)하다.

  ```ts
  interface Animal {
    name: string;
  }

  interface Dog extends Animal {
    breed: string;
  }

  const myDog: Dog = { name: "Max", breed: "Labrador" };
  ```

<br>

**Type Alias**

- 타입에 이름을 붙이는 데 사용하며, 인터페이스보다 더 다양한 타입을 정의할 수 있다.

  ```ts
  type Point = {
    x: number;
    y: number;
  };

  const point: Point = { x: 10, y: 20 };
  ```

- Union/Intersection과 같은 고급 타입 표현에 더 유용하다.

  ```ts
  type Animal = "Dog" | "Cat" | "Bird";
  const pet: Animal = "Cat";
  ```

- 유니온 타입은 하나 이상의 타입을 가질 수 있는 타입이다. 여러 타입을 `|` 연산자로 결합하여 사용한다.

<br>

**Implements**

- 클래스가 특정 인터페이스를 구현하도록 강제한다.

  ```ts
  interface Animal {
    name: string;
    makeSound(): void;
  }

  class Dog implements Animal {
    name: string;
    constructor(name: string) {
      this.name = name;
    }
    makeSound() {
      console.log("Woof!");
    }
  }
  ```

<br>
<br>

### <mark class="yellow">타입 좁히기</mark>

유니온 타입(ex: `string|number`) 또는 더 넓은 타입을 조건문 등을 이용해 더 구체적인 타입으로 제한하는 것을 말한다.

1\. `typeof` 사용  
기본 자료형(`string`, `number`, `boolean`) 등을 좁힐 때 사용된다.

```js
if (typeof value === "string") {
}
```

<br>

2\. `instanceof` 사용  
객체가 특정 클래스의 인스턴스인지 확인할 때 사용된다.

```js
function makeSound(animal: Dog | Cat) {
  if (animal instanceof Dog) {
    animal.bark(); // Dog 타입으로 좁혀짐
  } else {
    animal.meow(); // Cat 타입으로 좁혀짐
  }
}
```

<br>

3\. `in` 연산자 사용  
객체에 특정 속성이 존재하는지 확인할 때 사용된다.

```js
type Fish = { swim: () => void };
type Bird = { fly: () => void };

function move(animal: Fish | Bird) {
  if ("swim" in animal) {
    animal.swim();
  } else {
    animal.fly();
  }
}
```

<br>

4\. 커스텀 타입 가드  
타입 가드 함수를 사용하여 타입을 좁히는 방식이다. 함수의 반환 타입에 `value is Type`을 사용하여 특정 타입임을 명시한다.

```js
type Circle = { kind: "circle" };
type Rectangle = { kind: "rectangle" };

function isCircle(shape: Circle | Rectangle): shape is Circle {
  return shape.kind === "circle";
}
```

<br>

5\. 리터럴 타입 체크

```js
type Action = { type: "ADD", payload: number } | { type: "DELETE", id: string };

function handleAction(action: Action) {
  if (action.type === "ADD") {
    console.log("Adding:", action.payload);
  } else if (action.type === "DELETE") {
    console.log("Deleting ID:", action.id);
  }
}
```

<br>
<br>

### <mark class="yellow">선언과 단언</mark>

**선언과 단언의 차이점**

1\. 타입 선언  
변수의 타입을 명시적으로 지정하는 방식이다.

```js
const name: string = "Alice"; // string 타입 선언
```

2\. 타입 단언  
개발자가 변수의 타입을 직접 단언하며, 타입스크립트는 이를 신뢰하여 타입 검사를 생략한다.  
`as` 키워드를 사용한다.

```js
const value: unknown = "Alice";
const name = value as string; // 타입 단언
```

<br>

**단언했을 때의 문제점**

1\. 런타임 문제  
단언은 타입스크립트가 타입 검사를 생략하므로 잘못된 단언은 런타임 오류를 유발할 수 있다.

```js
const value: unknown = 123;
const name = value as string; // 잘못된 단언
console.log(name.toUpperCase()); // 런타임 오류 발생
```

2\. 컴파일 문제  
타입 단언은 타입스크립트의 타입 검사를 우회하기 때문에, 잘못된 단언이 있어도 컴파일러가 이를 잡아내지 못한다.

```js
const value: number = 42;
const name = value as string; // 컴파일러는 오류를 잡지 못함
```

<br>

**단언을 사용하는 상황**

💡 타입 스크립트가 타입을 안전하게 추론할 수 있는 경우에는 단언을 피해야한다.

1\. 타입스크립트가 타입을 추론하지 못 하는 경우  
ex) DOM 조작

```js
const input = document.querySelector("input") as HTMLInputElement;
input.value = "Hello";
```

2\. 데이터가 특정 타입임을 확인하는 경우  
ex) API 응답 데이터

```js
const response: unknown = fetchData();
const data = response as MyType;
```

3\. 외부 데이터를 가져오는 경우
ex) JSON 데이터와 Storybook의 Meta 타입을 만족시키기 위해

JSON 파일에서 가져온 데이터는 타입스크립트의 타입 체킹 없이 일반적인 객체 형태로 읽힌다. 따라서 특정 리터럴 타입으로 정의됐을 경우 이를 인식하지 못하고 `string`으로 추론된다. 이때 타입 단언을 사용해 컴파일러에게 올바른 타입임을 명시한다.

<br>
<br>

## <mark class="pink">📖E2E 테스트 (with Cypress)</mark>

### <mark class="yellow">E2E 테스트</mark>

- 최대한 실제 사용자가 앱을 사용하듯이 작성하여, 실제 사용할 때 생길 수 있는 문제를 사전에 검증할 수 있도록 한다.
- 실제 사용성을 그대로 테스트할 수 있는 큰 장점이 있는 대신 실제 프로덕션 앱을 개발하는 상황을 가정하자면 단위 테스트에 비해 작성하는 비용이 큰 편이다. 그만큼 앱에서 가장 핵심이 되는 기능 플로우를 잘 선정하여 해당 기능에 문제가 생기지 않는지를 검증하기 위한 장치로 활용합니다.
- 잘 작성해둔다면 개발자 및 기획자, 디자이너, QA 등이 직접 수동으로 기능을 테스트해보아야 하는 수고를 매우 크게 덜 수 있습니다.

<br>
<br>

### <mark class="yellow">Cypress</mark>

**설명**

- E2E 테스트 뿐만 아니라 **실제 브라우저에서 동작하는 방식**을 테스트 해야 하는 모든 경우에 활용할 수 있다.
- E2E 테스트, 통합 테스트, 단위 테스트, API 테스트 등
- GUI 도구를 지원하며, 테스트의 각 단계 및 결과를 브라우저에서 시각적으로 확인할 수 있다.

<br>

### <mark class="yellow">Cypress 시작하기</mark>

1\. 프로덕션 앱 로컬에서 띄우기  
실제 접속할 페이지가 존재하게끔 `index.html`을 로컬 서버를 이용하여 띄워주어야 한다.

```bash
npm start
```

<br>

2\. cypress/e2e2/ 하위에 [name].cy.js 파일 만들기

**cypress/e2e2/App.cy.js** 같은 파일을 만들어 테스트 코드를 작성한다.

<br>

3\. 프로덕션 코드 작성하기

**index.html**

```html
<input type="text" name="nickname" id="nickname" />
<button type="button" id="add-button">추가</button>
<ul class="nickname-list"></ul>
```

<br>

4\. 테스트 코드 - 페이지 방문

```js
cy.visit("http://localhost:8080/");
```

<br>

5\. 테스트 코드 - 필요한 엘리먼트 찾기, 테스트 대상 이벤트 발생시키기

click, type, scroll, focus 등등 실제로 사용자가 앱을 사용할 때 하는 행동을 대부분 그대로 발생시킬 수 있다.

localStorage 같은 브라우저에서 자체적으로 제공해주는 API도 당연히 사용할 수 있다.

```js
cy.get("#nickname").type(nickname);
cy.contains("추가").click();
```

<br>

5\. 테스트 코드 - 단언

```js
cy.get(".nickname-list").children().last().should("contain.text", nickname);
```

<br>
<br>

### <mark class="yellow">Cypress 참고 API</mark>

**엘리먼트 찾기 예시**

1\. 클래스로 찾기

```js
// 클래스가 'plus-button' 인 엘리먼트
cy.get(".plus-button");
```

<br>

2\. 텍스트로 찾기

```js
// '+' 텍스트를 포함하는 엘리먼트
cy.contains("+");
```

<br>

**단언 예시**

```js
// 보임
cy.get(".login").should("be.visible");

// 'form-horizontal' 클래스가 있음
cy.get("form").should("have.class", "form-horizontal");

// value가 'Jane'이 아님
cy.get("input").should("not.have.value", "Jane");

// 텍스트가 '10'
cy.get(".value").should("have.text", "10");

// disabled 속성이 있음(true)
cy.get(".btn-inc").should("have.attr", "disabled");
```
