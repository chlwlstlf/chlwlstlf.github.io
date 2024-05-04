---
layout: single
title: "[React]리액트 렌더링"
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
  ```html
  <li key="{index}">{index}</li>
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

- 일반적으로 렌더링 결과물은 JSZ 문법으로 구성되어 있다.

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
