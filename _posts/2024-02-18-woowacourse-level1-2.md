---
layout: single
title: "[우테코]Level1 Data Types, Scope, Object, Prototype"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션1 LMS 강의 자료2

## <mark class="pink">📚JavaScript Data Type</mark>

**<mark class="yellow">Primitive Type(원시 값, 기본형)</mark>**

**종류**  
Number, String, Boolean, null, undefined, Symbol

**불변셩**  
한 번 생성된 원시 값은 읽기 전용인 값으로써 변경할 수 없다.  
어느 한 쪽에서 재할당을 통해 값을 변경하더라고 서로 간섭할 수 없다.

**값에 의한 전달**  
변수에 원시 값을 갖는 변수를 할당하면 원시 값이 복사된다.  
하지만 서로 **다른 메모리 공간**에 저장된 별개의 값이다.

```js
var age = 30;
var copy = age;
console.log(age); // 30
console.log(copy); // 30

age = 20;
console.log(age); // 20
console.log(copy); // 30(불변성)
```

<br>

**<mark class="yellow">Reference Type(참조형)</mark>**

**종류**  
Function, Date, Array, RegExp, Map, WeakMap, Set, WeakSet

**변경 가능한 값**  
객체는 프로퍼티 개수가 정해져 있지 않으며, 동적으로 추가되고 삭제될 수 있다.  
객체는 메모리에 저장된 객체를 직접 수정할 수 있다.

**참조에 의한 전달**  
객체를 할당한 변수는 동적으로 변할 수 있는 메모리 공간에 있는 데이터의 **주소**를 가리키는 참조값을 갖는다.  
여러 개의 식별자가 하나의 객체를 공유한다.  
아래 예시에서 user와 copy는 같은 객체를 참조한다.

```js
var user = {
  name: "jinsil",
};

var copy = user;
```

<br>
<br>

## <mark class="pink">📚Execution Context & Scope</mark>

**<mark class="yellow">호이스팅</mark>**

**정의**  
어떤 실행 컨텍스트가 활성화되는 시점에서 선언된 변수를 위로 끌어올리는 것

<br>

**변수 호이스팅**  
호이스팅 때문에 자스가 전체 코드를 스캔하면서 변수 같은 정보를 실행 컨텍스트에 기록해놓는다.  
실행 컨텍스트를 실행하고 선언문만 먼저 실행한다.

1\. var

- 선언하면서 식별자를 undefined로 초기화한다.
- 선언 라인 위에서 console 찍었을 때 에러는 안 뜨고 `undefined`를 출력한다.

2\. let, const

- let, const는 선언만 한다.
- 유효한 값을 읽어오지 못 해 참조 에러가 뜬다. 그래서 `일시적 사각지대`가 생긴다.

<br>

**함수 호이스팅**

1\. 함수 표현식 → 변수 호이스팅과 똑같이 동작

- var로 선언된 함수 또한 undefined로 초기화 되어있고 함수와 달리 호출될 수 없기 때문에 `undefined is not a function`이라는 타입 에러가 발생한다.
- let, const로 선언하면 초기화되어 있지 않아 `Cannot read its value yet`이라는 참조 에러가 발생한다.

2\. 함수 선언식

- 함수 선언과 동시에 완성된 함수 객체를 생성해서 환경 레코드에 기록해둔다.
- 선언 전에도 함수를 사용할 수 있다. (사용 지양해야할 듯)

<br>

**<mark class="yellow">Execution Context (실행 컨텍스트)</mark>**

[하루의 실행 컨텍스트](https://www.youtube.com/watch?v=EWfujNzSUmw&t=5s)

**정의**  
실행할 코드에 제공할 환경 정보들을 모아놓은 객체

**동작**  
호이스팅  
외부 환경 구성  
this 값 설정  
자동으로 생성되는 전역 공간과 eval을 제외하면 함수를 실행할 때 생성된다.

<br>

**<mark class="yellow">Scope</mark>**

[엘라의 Scope & Closure](https://www.youtube.com/watch?v=PVYjfrgZhtU)  
[루루의 Scope & Closure](https://www.youtube.com/watch?v=xJtVVLPxgco)

**정의**  
현재 접근할 수 있는 변수들의 범위

**설명**  
어떤 변수가 스코프 안에 선언되었으면 밖에서는 해당 변수에 접근 불가능  
블록에 의해서도 스코프 경계가 발생  
자바스크립트의 모든 함수는 상위 스코프를 기억한다.

**Scope 레벨**  
블록 레벨 스코프: if문, for문, 함수 ...(Javascript에서 블록 스코프를 사용하기 위해 let과 const를 도입)  
함수 레벨 스코프: 함수

**Scope 종류**  
동적 스코프: 함수가 호출되는 시점에 결정  
정적 스코프(렉시컬 스코프): 함수가 정의되는 시점에 결정, Javascript에서 함수는 생성되면서 자신의 내부 슬롯에 상위 스코프의 참조를 저장한다.

**식별자 결정**  
코드에서 변수나 함수의 값을 결정하는 것

**Scope Chain**  
식별자의 유효범위를 안에서부터 바깥으로 차례로 검색해 나가는 것  
현재 호출된 함수가 선언될 당시의 렉시컬 환경을 참조  
선언하다라는 행위가 실제로 일어날 수 있는 시점이란 콜 스택 상에서 어떤 실행 컨텍스트가 활성화된 상태일 뿐

<br>

**<mark class="yellow">this</mark>**

TODO

<br>

**<mark class="yellow">Closure</mark>**

**정의**  
한 중첩 함수가 상위 스코프의 식별자를 참조하고 있고 본인의 외부 함수보다 더 오래 살아있는 것  
생명주기가 종료된 외부 함수의 식별자를 참조해야 한다.

**실행 컨텍스트와 함수**  
함수 실행이 끝나면 콜 스택에서 제거되며 메모리에서도 사라진다. 하지만 클로저를 통해 변수가 참조되는 경우, 실행 컨텍스트의 일부 데이터가 여전히 메모리에 유지된다.

**설명**  
이 생명주기를 마감한 outer 함수 즉 상위 함수의 변수를 inner에서 참조할 수 있다면 inner 함수가 클로저

inner 함수가 생성될 때, 상위 함수(outer)의 스코프를 기억하는 클로저 객체를 함께 생성한다.

outer 함수가 생명주기가 끝나면서 실행 컨텍스트 스택에서 제거가 되었지만 inner 함수는 상위 스코프 참조를 유지하고 있기 때문에 이를 참조하여 생명주기가 끝난 외부함수의 변수에 접근이 가능하다.

inner 함수가 반환되면서, outer의 스코프 정보는 inner 함수의 클로저로 메모리에 유지된다.

```js
var outer = function () {
  var a = 1;
  var inner = function () {
    return a++;
  };
  return inner;
};

var closure = outer(); // outer 실행 및 종료, 콜 스택에서 제거돼도 스코프 정보가 메모리에 남아 있음
closure(); // outer는 반환된 inner함수이므로 inner함수는 언젠가 호출된 가능성이 있음 → 클로저
```

**클로저가 아닌 경우**  
1\. 상위 스코프의 식별자를 참조하고 있지 않는 경우 → 상위 스코프를 기억하지 않는다.  
2\. outer 외부로 inner를 반환하지 않는 경우 → inner의 생명 주기가 더 짧다.

<br>
<br>

## <mark class="pink">📚Object</mark>

**<mark class="yellow">Object</mark>**

자신의 속성을 가지고 있고 다른 것과 식별 가능한 것

속성과 메소드로 구성되어 있다.

<br>

**<mark class="yellow">객체와 원시값</mark>**

원시값은 값으로 저장되고, 원시값을 복사하면 그대로 복사가 된다.

객체는 참조가 저장되므로 객체를 복사하면 참조만 복사된다.

객체는 서로 동일한 객체를 참조하고 있을 때만 같은 것으로 본다.

<br>

**<mark class="yellow">객체와 Prototype</mark>**

객체의 속성을 참조하면 먼저 해당 객체에 속성이 참조되어 있는지 확인한다.

참조되어 있지 않으면 생성자 함수의 prototype 속성에서 찾는다.

여기서도 찾을 수 없으면 prototype은 객체이고, 객체는 Object() 생성자로부터 만들어지므로 Object() 생성자의 prototype 속성에서 속성을 찾는다.

여기서도 속성을 찾을 수 없으면 이 속성은 정의되지 않았다고 판단한다.(undefined)

prototype 속성은 Object() 객체라는 사실과 객체 속성 검색 체인(=프로토타입 상속) 덕분에 모든 객체는 Object()를 상속받는다.

<br>

**<mark class="yellow">함수와 객체에서의 스코프</mark>**

함수는 1급 객체다. (1급 객체: 다른 객체들에 일반적으로 적용 가능한 연산을 모두 지원하는 객체)

함수 안에서 사용하는 this 키워드는 함수를 포함한 객체를 참조한다. this의 값은 함수가 호출되는 컨텍스트에 따라 결정된다.

함수는 스코프 체인을 통해 변수를 찾는다.

한 번 작성된 함수는 다른 컨텍스트에서 실행되더라도 원래의 스코프에 접근할 수 있다. 이러한 특성으로 인해 클로저가 만들어진다.

<br>

**<mark class="yellow">자바스크립트에서의 객체 지향</mark>**

자바스크립트는 함수 기반 언어인 Scheme을 기반으로 만들어진 뒤 자바에서 다양한 프로그래밍 개념들을 가져왔다. 객체지향 언어는 아니지만 프로토타입을 통해 비슷하게 구현할 수 있다.

기본 객체: Object, Function, Array

클래스 기반x, 프로토타입 기반o

<br>
<br>

## <mark class="pink">📚Prototype</mark>

[Object prototypes](https://developer.mozilla.org/ko/docs/Learn/JavaScript/Objects/Object_prototypes)  
[아놀드의 프로토타입 뽀개기](https://www.youtube.com/watch?v=TqFwNFTa3c4)

**<mark class="yellow">프로토타입</mark>**

**설명**  
자바스크립트는 프로토타입 기반 언어  
모든 객체들이 메소들와 속성들을 상속 받기 위한 템플릿으로써 프로토다입 객체를 가진다.  
어떤 객체를 원형으로 삼고 이를 복제함으로써 상속과 비슷한 효과를 얻는다.  
상속되는 속성과 메소드들은 각 객체가 아니라 객체의 생성자의 `prototype`이라는 속성에 정의되어 있다.

**동작**  
new 연산자로 constructor를 호출하면 instance가 만들어진다.  
이 instance가 가지고 있는 생략 가능한 프로퍼티인 `__proto__`는 constructor의 prototype을 참조한다.

**proto 접근자 프로퍼티**  
모든 객체는 proto 접근자 프로퍼티를 통해 자신의 프로토타입, 즉 [[Prototype]] 내부 슬롯에 간접적으로 접근할 수 있다.

**프로토타입 체인**

어떤 객체에 특정 프로퍼티나 메서드에 접근할 때 특정 프로퍼티나 메서드가 없다면 내부 링크를 통해 상위 프로토타입으로 접근하는 행위

자바스크립트의 모든 프로토타입 체인은 Object.prototype에서 끝나게 된다. Object.prototype은 프로토타입의 종점

Object도 결국엔 Object.prototype을 가지는 함수 객체이다. 따라서 `Object.__proto`는 `Function.prototype`이다.

`Function.prototype.__proto__`는 Object 생성자 함수에 의해 생성된 객체이기 때문에 `Object.prototype`이다.

[프로토타입체인2]

```js
var person1 = new Person("Jinsil", 25);
person1.valueOf();
```

1\. 브라우저는 우선 `person1` 객체가 `valueOf()` 메소드를 가지고 있는지 체크한다.  
2\. 없으므로 `person1`의 프로토타입 객체(`Person()` 생성자의 프로토타입)에 `valueOf()` 메소드가 있는지 체크한다.
3\. 여전히 없으므로 `Person()` 생성자의 프로토타입 객체의 프로토타입 객체(`Object()` 생성자의 프로토타입)가 `valueOf()` 메소드를 가지고 있는지 체크한다. 여기에 있으니 호출하며 끝난다.

[프로토타입체인1]

**함수 객체**  
함수 객체의 `__proto__`는 `Function.prototype`이다.  
그리고 `prototype` 프로퍼티도 가지고 생성된다.

그 이유는?

생성자 함수라는 것이 있는데 이는 new 연산자를 붙여 실행하는 함수이다. (함수 첫 글자는 대문자로 시작해야하는 관례도 있다.)  
그럼 그 함수로 생성된 인스턴스의 `__proto__`는 무엇일까? 상위의 prototype을 가리켜야하고 이는 **생성자 함수 prototype**이다.

예를 들어 `function Person(name) {this.name = name;}` 생성자 함수로  
`const Jinsil = new Person('jinsil')` 인스턴스를 생성했다고 하자.  
이때 `Jinsil.__proto__`는 `Person.prototype`이고 이를 위해 생성자 함수 Person의 자체 prototype 프로퍼티도 같이 생성되는 것이다.

<br>

**<mark class="yellow">Mixin</mark>**

다른 객체를 상속받을 필요 없이 구현되어있는 메서드를 담고 있는 객체

특정 기능을 별도의 객체로 분리해 두고, 여러 클래스나 객체에 재사용할 수 있도록 하는 패턴

```js
// Mixin: 다른 객체에 추가될 메서드를 담고 있는 객체
const loggableMixin = {
  log(message) {
    console.log(`[LOG]: ${message}`);
  },
};

const user = {
  name: "Jinsil",
  age: 25,
};

Object.assign(user, loggableMixin); // 객체 조합

user.log("This is Alice's log message.");
// 출력: [LOG]: This is Alice's log message.
```

<br>
<br>

## <mark class="pink">📚추가 학습</mark>

**<mark class="yellow">자바스크립트에서 데이터 타입은 왜 필요한가?</mark>**

- 값을 저장할 때 확보해야 하는 메모리 공간의 크기를 결정하기 위해
- 값을 참조할 때 한 번에 읽어 들여야 할 메모리 공간의 크기를 결정하기 위해
- 메모리에서 읽어 들인 2진수를 어떻게 해석할지 결정하기 위해

<br>

**<mark class="yellow">왜 화살표 함수를 쓰는 방식이 지향되고 있는가?</mark>**

- function 키워드가 return 문이 사라지고, 1줄짜리 함수에서 중괄호 없이 간결하게 작성할 수 있다.
- 렉시컬 this를 사용하여 호출 컨텍스트를 고정해서 this 바인딩을 따로 해줄 필요가 없다.
- 고차 함수의 콜백 함수로 자주 사용되며 이로 인해 가독성이 좋아졌다.

<br>

**일반 함수를 써야할 때는?**

- 객체의 메서드를 정의할 때
- 생성자 함수 만들 때 (화살표 함수는 생성자 함수로 사용할 수 없다)
