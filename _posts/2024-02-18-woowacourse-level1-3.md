---
layout: single
title: "[우테코]Level1 Data Types, Scope, Object, Prototype"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션1 LMS 강의 자료2

## <mark style='background-color: #ffdce0'>📚 JavsScript Data Type</mark>

**1\. Primitive Type(원시 값, 기본형)**

- Number, String, Boolean, null, undefined, Symbol
- 변경 불가능한 값, 한 번 생성된 원시 값은 읽기 전용인 값으로써 변경할 수 없다.
- 값에 의한 전달: 변수에 원시 값을 갖는 변수를 할당하면 원시 값이 복사된다. 하지만 서로 다른 메모리 공간에 저장된 별개의 값이다.
- 어느 한 쪽에서 재할당을 통해 값을 변경하더라고 서로 간섭할 수 없다. → 불변성

```js
var age = 30;
var copy = age;

console.log(age); // 30
console.log(copy); // 30

age = 20;

console.log(age); // 20
console.log(copy); // 30
```

<br/>
<br/>

**2\. Reference Type(참조형)**

- 객체는 프로퍼티 개수가 정해져 있지 않으며, 동적으로 추가되고 삭제될 수 있다. → 변경 가능한 값
- 객체를 할당한 변수는 동적으로 변할 수 있는 메모리 공간에 있는 데이터의 주소를 가리키는 참조값을 갖는다.
- 객체는 메모리에 저장된 객체를 직접 수정할 수 있다.
- 참조에 의한 전달: 여러 개의 식별자가 하나의 객체를 공유

## <mark style='background-color: #ffdce0'>📚 Execution Context & Scope</mark>

**1\. Execution Context (실행 컨텍스트)**

정의

- 실행할 코드에 제공할 환경 정보들을 모아놓은 객체

동작

- hoisting: 선언된 변수를 위로 끌어올리는 것
- 외부 환경 구성
- this 값 설정
- 전역 공간과 eval을 제외하면 함수를 실행할 때 생성된다.

<br/>

**2\. Scope**

정의

- 현재 접근할 수 있는 변수들의 범위

설명

- 어떤 변수가 스코프 안에 선언되었으면 밖에서는 해당 변수에 접근 불가능
- 블록에 의해서도 스코프 경계가 발생

Scope Chain

- 식별자의 유효범위를 안에서부터 바깥으로 차례로 검색해 나가는 것
- 현재 호출된 함수가 선언될 당시의 렉시컬 환경을 참조

<br/>
<br/>

## <mark style='background-color: #ffdce0'>📚 Object</mark>

**1\. Object**

정의

- 자신의 속성을 가지고 있고 다른 것과 식별 가능한 것
- 속성과 메소드

<br/>

**2\. 객체와 원시 값**

- 원시값은 값으로 저장되고, 원시값을 복사하면 그대로 복사가 된다.
- 객체를 참조가 저장되므로 객체를 복사하면 참조만 복사된다.
- 객체는 서로 동일한 객체를 참조하고 있을 때만 같은 것으로 본다.

<br/>

**3\. 객체와 Prototype**

- 객체의 속성을 참조하면 먼저 해당 객체에 속성이 참조되어 있는지 확인한다.
- 참조되어 있지 않으면 생성자 함수의 prototype 속성에서 찾는다.
- 여기서도 찾을 수 없으면 prototype은 객체이고, 객체는 Object() 생성자로부터 만들어지므로 Object() 생성자의 prototype 속성에서 속성을 찾는다.
- 여기서도 속성을 찾을 수 없으면 이 속성은 정의되지 않았다고 판단한다.(undefined)
- prototype 속성은 Object() 객체라는 사실과 객체 속성 검색 체인(=프로토타입 상속) 덕분에 모든 객체는 Object()를 상속받는다.

<br/>

**4\. 함수와 객체에서의 스코프**

- 함수는 1급 객체다. (1급 객체: 다른 객체들에 일반적으로 적용 가능한 연산을 모두 지원하는 객체)
- 함수는 스코프 체인을 통해 변수를 찾는다.
- 한 번 작성된 함수는 다른 컨텍스트에서 실행되더라도 원래의 스코프에 접근할 수 있다. 이러한 특성으로 인해 클로저가 만들어진다.

<br/>

**5\. 자바스크립트에서의 객체 지향**

- 기본 객체: Object, Function, Array
- 클래스 기반x, 프로토타입 기반o

<br/>
<br/>

## <mark style='background-color: #ffdce0'>📚 Prototype</mark>

**1\. 프로토타입**

설명

- 자바스크립트는 프로토타입 기반 언어
- 어떤 객체를 원형으로 삼고 이를 복제함으로써 상속과 비슷한 효과를 얻는다.

동작

- new 연산자로 constructor를 호출하면 instance가 만들어진다.
- 이 instance가 가지고 있는 생략 가능한 프로퍼티인 `__proto__`를 constructor의 prototype을 참조한다.

<br/>

**2\. Mixin**

- 다른 객체를 상속받을 필요 없이 구현되어있는 메서드를 담고 있는 객체
- 미션에서 보통 console 함수가 그렇다.
