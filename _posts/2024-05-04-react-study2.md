---
layout: single
title: "[React]리액트 파이버, 렌더링"
categories: react
toc: true
toc_sticky: true
---

# 리액트 스터디2 - 가상 DOM, 리액트 파이버, 렌더링 과정

참고 자료-리액트 Deep Dive

## <mark style='background-color: #ffdce0'>2.2 가상 DOM과 리액트 파이버</mark>

**1\. DOM과 브라우저 렌더링 과정**

<mark style='background-color: #fff5b1'>DOM이란?</mark>

- 웹페이지에 대한 인터페이스로 브라우저가 웹페이지의 콘텐츠와 구조를 어떻게 보여줄지에 대한 정보를 담고 있다.

<br>

<mark style='background-color: #fff5b1'>화면 그리는 과정</mark>

1. 브라우저가 사용자가 요청한 주소를 방문해 HTML 파일을 다운로드한다.
2. 브라우저의 렌더링 엔젠은 HTML을 파싱해 DOM노드로 구성된 트리(DOM)를 만든다.
3. 2번 과정에서 CSS 파일을 만나면 해당 css 파일도 다운로드한다.
4. 브라우저의 렌더링 엔진은 이 CSS도 파싱해 CSS노드로 구성된 트리(CSSOM)를 만든다.
5. 브라우저는 2번에서 만든 DOM 노드를 순회하는데, 여기서 모든 노드를 방문하는 것이 아니고 사용자 눈에 보이는 노드만 방문한다.  
   (display: none은 방문해 작업하지 않는다. → 트리 분석 과정 빨라짐)
6. 5번에서 제외된 눈에 보이는 노드를 대상으로 해당 노드에 대한 CSSOM 정보를 찾고 여기서 발견한 CS 스타일 정보를 이 노드에 적용한다.

※ CSS 적용 과정

- 레이아웃: 좌표 계산
- 페인팅: 색 등 유효한 모습 그리는 과정

<br>
<br>

**2\. 가상 DOM의 탄생 배경**

<mark style='background-color: #fff5b1'>가상 DOM이란?</mark>

- 실제 브라우저의 DOM이 아닌 리액트가 관리하는 가상의 DOM
- 웹페이지가 표시해야 할 DOM을 일단 메모리에 저장하고 리액트가 실제 변경에 대한 준비가 완료됐을 때 실제 브라우저의 DOM에 반영한다.
- DOM 계산을 브라우저가 아닌 메모리에서 계산하는 과정을 한 번 거치게 된다면 실제로는 여러 번 발생했을 렌더링 과정을 최소화할 수 있고 브라우저와 개발자의 부담을 덜 수 있다.

<br>
<br>

**3\. 가상 DOM을 위한 아키텍처, 리액트 파이버**

<mark style='background-color: #fff5b1'>리액트 파이버란?</mark>

- 리액트에서 관리하는 평범한 자바스크립트 객체
- 파이버 재조정자: 가상 DOM과 실제 DOM을 비교해 변경 사항을 수집하며, 만약 이 둘 사이에 차이가 있으면 변경에 관련된 정보를 가지고 있는 파이버를 기준으로 화면에 렌더링을 요청하는 역할

<br>

<mark style='background-color: #fff5b1'>파이버가 하는 일</mark>

- 작업을 작은 단위로 분할하고 쪼갠 다음, 우선순위를 매긴다.
- 이러한 작업을 일시 중지하고 나중에 다시 시작할 수 있다.
- 이전에 했던 작업을 다시 재사용하거나 필요하지 않은 경우에는 폐기할 수 있다.
- 이 모든 건 비동기로 일어난다.

<br>

<mark style='background-color: #fff5b1'>파이버는 어떻게 구현돼 있을까?</mark>

- 하나의 작업 단위
- 리액트는 이러한 작업 단위를 하나씩 처리하고 finishedWork()라는 작업으로 마무리한다.
- 렌더 단계에서는 리액트를 사용자에게 노출되지 않는 모든 비동기 작업을 수행한다. 파이버는 우선순위 지정, 중지 등의 작업을 한다.
- 커밋 단계에서는 DOM에 실제 변경 사항을 반영하기 위한 작업, commitWork()가 실행된다. 동기식으로 일어나고 중단될 수 없다.

<br>

<mark style='background-color: #fff5b1'>파이버 내부 코드</mark>

- 리액트 요소는 렌더링이 발생할 때마다 새롭게 생성되지만 파이버는 가급적이면 재사용이 된다.
- 컴포넌트가 최초로 마운트되는 시점에 생성되어 이후에는 가급적이면 재사용된다.

<br>

<mark style='background-color: #fff5b1'>1. tag</mark>

- element에 1:1로 매칭된 정보를 가지고 있는 것

<br>

<mark style='background-color: #fff5b1'>2. stateNode</mark>

- 파이버 자체에 대한 참조, 리액트는 이것으로 파이버와 관련된 상태에 접근한다.

<br>

<mark style='background-color: #fff5b1'>3. child, sibling, return</mark>

- 파이버 간의 관계를 나타내는 속성
- 파이버도 트리 형식인데 그 정보가 여기에 정의된다
- 파이버는 children이 없다. 하나의 child만 존재한다.

예시코드

```html
<ul>
  <li>하나</li>
  <li>둘</li>
  <li>셋</li>
</ul>
```

```js
const l3 = {
  return: ul, //return은 부모의 파이버
  index: 2, //여러 형제들 사이에서 자신의 위치가 몇 번째인지 숫자로 표현된다.
};

const l2 = {
  sibling: l3,
  return: ul,
  index: 1,
};

const l1 = {
  sibling: l2,
  return: ul,
  index: 0,
};

const ul = {
  child: l1,
};
```

<br>

<mark style='background-color: #fff5b1'>5. index, pendingProps, memoizedProps</mark>

- index: 여러 형제들 사이에서 자신의 위치가 몇 번째인지 숫자로 표현된다.
- pendingProps: 아직 작업을 미처 처리하지 못 한 props
- memoizedProps: pendingProps을 기준으로 렌더링이 완료된 이후에 pendingProps를 memoizedProps로 저장해 관리한다.

<br>

<mark style='background-color: #fff5b1'>6. updateQueue</mark>

- 상태 업데이트, 콜백 함수, DOM 업데이트 등 필요한 작업을 담아두는 큐

```js
type UpdateQueue = {
  first: Update | null
  last: Update | null
  hasForceUpdate: boolean
  callbackList: null | Array<Callback>
}
```

<br>

<mark style='background-color: #fff5b1'>7. memoizedState, alternate</mark>

- memoizedState: 함수 컴포넌트의 훅 목록이 저장된다.
- alternate: 반대편 트리 파이버

<br>
<br>

<mark style='background-color: #fff5b1'>리액트 파이버 트리</mark>

- 더블 버퍼링: 보이지 않은 곳에서 그다음으로 그려야 할 그림을 미리 그린 다음, 이것이 완성되면 현재 상태를 새로운 그림으로 바꾸는 기법
- 더블 버퍼링을 위해 트리가 두 개 존재하며, 커밋 단계에서 수행된다.

![1](https://velog.velcdn.com/images/tlsakch510/post/3cb5f069-e2e7-4f94-82e8-d1016a76293a/image.png)

1. current Tree를 기준으로 UI 렌더링을 한다.
2. 업데이트가 발생하면 workInProgress Tree를 빌드하기 시작한다.
3. workInProgress Tree를 빌드하는 작업이 끝나면 다음 렌더링에 이 트리를 사용한다.
4. 이 workInProgress Tree가 UI에 최종적으로 렌더링되어 반영이 완료되면 current가 이 workInProgress로 변경된다.

<br>
<br>

<mark style='background-color: #fff5b1'>파이버의 작업 순서</mark>

1. 리액트는 beginWork() 함수를 실행해 파이버 작업을 수행하는데, 더 이상 자식이 없는 파이버를 만날 때까지 트리 형식으로 시작된다.
2. 1번에서 작업이 끝난다면 그다음 completeWork() 함수를 실행해 파이버 작업을 완료한다.
3. 형제가 있다면 형제로 넘어간다.
4. 2번, 3번이 모두 끝났다면 return으로 돌아가 자신의 작업이 완료됐음을 알린다.

![Animated GIF](https://mblogthumb-phinf.pstatic.net/MjAyMzA4MjdfMjY2/MDAxNjkzMTQyMzk1NzA3.vGFIUCVN_n1bmYEQFQCozkLbxE4ZuQ0KrUkaMflXRpYg.iSWFoPLgk8GBLRLXhIhSkQfexUrsGO143vlYX7Lr-gsg.GIF.dlaxodud2388/%25ED%2594%2584%25EB%25A0%2588%25EC%25A0%25A0%25ED%2585%258C%25EC%259D%25B4%25EC%2585%25981.gif?type=w800)
[출처]https://m.blog.naver.com/dlaxodud2388/223195103660

<br>
<br>

**4\. 파이버와 가상 DOM**

- 파이버: 리액트 컴포넌트에 대한 정보를 1:1로 가지고 있는 것, 리액트 아키텍처 내부에서 비동기로 이뤄진다.
- 가상 DOM과 파이버는 동일한 개념이 아니다.
- 가상 DOM을 구현하기 위해 리액트 파이버가 만들어진 것이다.
- UI를 값으로 관리하고 이러한 흐름을 효율적으로 관리하기 위한 메커니즘이 리액트의 핵심이다.

<br>
<br>

## <mark style='background-color: #ffdce0'>2.4 렌더링은 어떻게 일어나는가?</mark>

**1\. 리액트의 렌더링이란?**

<mark style='background-color: #fff5b1'>리액트에서의 렌더링이란?</mark>

- 리액트 애플리케이션 트리 안에 있는 모든 컴포넌트들이 현재 자신들이 가지고 있는 props와 state의 값을 기반으로 어떻게 UI를 구성하고 이를 바탕으로 어떤 DOM 결과를 브라우저에 제공할 것인지 계산하는 일련의 과정

<br>
<br>

**2\. 리액트의 렌더링이 일어나는 이유**

<mark style='background-color: #fff5b1'>최초 렌더링</mark>

- 처음 애플리케이션에 진입했을 때

<br>

<mark style='background-color: #fff5b1'>리렌더링</mark>

- useState의 setter가 실행되는 경우: state가 업데이트 된다, 단순한 변수는 변경되어도 리렌더링을 발생시키지 않는다.

- useReducer의 dispatch가 실행되는 경우: 상태 업데이트하는 함수를 배열로 제공한다.

- key props가 변경되는 경우: key는 형제 요소들 사이에서 동일한 요소를 식별하는 값, 리렌더링 최소화하려면 필수로 해야하는 작업이다.
  ```jsx
  <li key={index}>{index}</li>
  ```

+) memo를 사용하면 리렌더링이 발생하지 않는다. 동일한 prop에 대해 리렌더링을 하는 것을 막을 수 있다. 다만, 콜백 함수를 prop으로 사용하는 컴퍼넌트에서 메모이징을 할 때 주의해야한다.

<br>
<br>

**3\. 리액트의 렌더링 프로세스**

<mark style='background-color: #fff5b1'>렌더링 과정</mark>

- 컴포넌트의 루트에서부터 차근차근 아래쪽으로 내려가면서 업데이트가 필요하다고 지정돼 있는 모든 컴포넌트를 찾는다.
- 업데이트가 필요한 컴포넌트를 발견하면 FunctionComponent() 그 자체를 호출한 뒤, 그 결과물을 저장한다.

<br>

<mark style='background-color: #fff5b1'>createElement</mark>

- 일반적으로 렌더링 결과물은 JSX 문법으로 구성되어 있다.

  ```jsx
  function Hello() {
    return (
      <TestComponent a={35} b="yceffor">
        안녕하세요
      </TestComponent>
    );
  }
  ```

- 위 JSX 문법은 다음과 같은 React.createElement를 호출해서 변환된다.

  ```js
  function Hello() {
    return React.createElement(
      TestComponent,
      {a: {35} b: 'yceffor'},
      '안녕하세요',
    )
  }
  ```

- 결과물은 브라우저의 UI 구조를 설명할 수 있는 일반적인 자바스크립트 객체를 반환한다.

  ```js
  {type: TestComponent, props: {a: {35} b: 'yceffor', children: "안녕하세요"}}
  ```

<br>
<br>

**4\. 렌더와 커밋**

<mark style='background-color: #fff5b1'>렌더 단계</mark>

- 컴포넌트를 렌더링하고 변경 사항을 계산하는 모든 작업
- 컴포넌트를 실행해 이 결과와 이전 가상 DOM을 비교하는 과정을 거쳐 변경이 필요한 컴포넌트를 체크하는 단계
- type, props, key를 비교한다.
- 이 중 하나라도 변경된 것이 있으면 변경이 필요한 컴포넌트로 체크해 둔다.

<br>

<mark style='background-color: #fff5b1'>커밋 단계</mark>

- 렌더 단계의 변경 사항을 실제 DOM에 적용해 사용자에게 보여주는 과정
- 이 단계가 끝나면 브라우저의 렌더링이 발생한다.
- 리액트가 먼저 DOM을 커밋 단계에서 업데이트한다면 이렇게 만들어진 모든 DOM 노드 및 인스턴스를 가리키도록 리액트 내부의 참조를 업데이트한다.
- 그다음, useLayoutEffect 훅을 호출한다.

- **리액트의 렌더링이 일어난다고 해서 무조건 DOM 업데이트가 일어나는 것은 아니다.**
- 렌더링을 수행했으나 커밋 단계까지 갈 필요가 없다면, 즉 변경 사항을 계산했는데 아무런 변경 사항이 감지되지 않는다면 이 커밋 단계는 생략될 수 있다.
- 커밋 단계가 생략되면 DOM 업데이트가 일어나지 않는다.

<br>

<mark style='background-color: #fff5b1'>동시성 렌더링</mark>

- 리액트의 렌더링은 항상 동기식으로 작동했다.
- 렌더링 과정이 길어질수록 애플리케이션의 성능 저하로 이어졌다.
- 하지만 비동기 방식으로 이뤄질 경우 일관된 UI를 사용자에게 보여줄 수 없다.

- 그럼에도 비동기 렌더링 시나리오는 몇 가지 상황에서 유효할 수도 있다.
- 의도된 우선순위로 컴포넌트를 렌더링해 최적화할 수 있는 비동기 렌더링(동시성 렌더링)이 리액트 18에서 도입됐다.
- 동시성 렌더링은 렌더 단계가 비동기로 작동해 우선수위를 조정하거나 렌더링을 중단, 재시작, 포기한다.
- 브라우저의 동기 작업을 차단하지 않고 백그라운드에서 새로운 리액트 트리를 준비한다.

<br>
<br>

**5\. 일반적인 렌더링 시나리오 살펴보기**

<mark style='background-color: #fff5b1'>예제 코드</mark>

A > B > C > D

```jsx
import { useState } from "react";

export default function A() {
  return (
    <div className="App">
      <h1>Hello React!</h1>
      <B />
    </div>
  );
}

function B() {
  const [counter, setCounter] = useState(0);

  function handleButtonClick() {
    setCounter((prev) => prev + 1);
  }

  return (
    <>
      <label>
        <C number={counter} />
      </label>
      <button onClick={handleButtonClick}>+</button>
    </>
  );
}

function C({ number }) {
  return (
    <div>
      {number} <D />
    </div>
  );
}

function D() {
  return <>리액트 재밌다!</>;
}
```

<mark style='background-color: #fff5b1'>B의 버튼을 눌렀을 때 렌더링 과정</mark>

1. B 컴포넌트의 setState가 호출된다.
2. B 컴포넌트의 리렌더링 작업이 렌더링 큐에 들어간다.
3. 리액트는 트리 최상단에서부터 렌더링 경로를 검사한다.
4. A 컴포넌트는 리렌더링이 필요한 컴포넌트로 표시돼 있지 않으므로 별다른 작업을 하지 않는다.
5. 그다음 하위 컴포넌트인 B 컴포넌트는 업데이트가 필요하다고 체크돼 있으므로 B를 리렌더링한다.
6. 5번 과정에서는 B는 C를 반환했다.
7. C는 props인 number가 업데이트됐다. 그러므로 업데이트가 필요한 컴포넌트로 체크돼 있고 업데이트한다.
8. 7번 과정에서 C는 D를 반환했다.
9. D도 마찬가지로 업데이트가 필요한 컴포넌트로 체크되지 않았다. 그러나 C가 렌더링됐으므로 그 자식인 D도 렌더링됐다.
