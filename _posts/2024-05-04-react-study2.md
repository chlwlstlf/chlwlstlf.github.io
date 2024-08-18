---
layout: single
title: "[React]리액트 파이버, 렌더링"
categories: react
toc: true
toc_sticky: true
---

# 리액트 스터디2 - 가상 DOM, 리액트 파이버, 렌더링 과정

## <mark style='background-color: #ffdce0'>📌리액트의 렌더링이란?</mark>

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>컴포넌트가 props와 state를 통해 UI를 어떻게 구성할지 컴포넌트에게 <strong>요청</strong>하는 작업</p>
  <p>React의 렌더링 ≠ DOM 업데이트</p>
</div>

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>React의 렌더링: UI를 생성하는 과정, 그 결과물은 가상 DOM에 생성, 즉. render 단계</p>
  <p>DOM 업데이트: 가상 DOM에 대한 변경 사항을 실제 DOM에 적용하는 과정, 즉. commit 단계</p>
  <p>→ React의 렌더링은 일어났지만 DOM 업데이트는 일어나지 않을 수 있다.</p>
</div>

React 내부에서 Render 단계는 더 좁은 의미로, JSX 또는 `React.createElement()`로 작성된 코드를 React 엘리먼트로 변경하는 작업만을 의미한다. React 엘리먼트는 클래스가 아닌 일반 객체로, 사용자가 작성한 컴포넌트 또는 엘리먼트 타입과 어트리뷰트, 자식에 관한 정보를 담고 있는 객체이다.

가상 DOM: 파이버 노드로 구성된 트리

![Untitled (8)](https://github.com/chlwlstlf/data/assets/63334368/0e00d9a0-dad3-4fcf-be37-dabbfc1d6f11)

## <mark style='background-color: #ffdce0'>📌1. 트리거 단계</mark>

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <div>컴포넌트가 그려지기 위해서는 어떤 액션, 즉 "트리거"가 필요합니다.</div>
</div>

### 최초 렌더링

처음 애플리케이션에 진입했을 때

### 리렌더링

1\. **useState의 setter가 실행되는 경우**

```jsx
const [count, setCount] = useState(0); //state 업데이트 → 리렌더링
```

```jsx
let count = 0; //단순한 변수 업데이트 → 리렌더링X
```

2\. **useReducer의 dispatch가 실행되는 경우**

- **`dispatch`** 함수를 호출하면 해당 액션에 따라 상태가 변경된다.

```jsx
const [state, dispatch] = useReducer(reducer, initialState);
```

3\. **key props가 변경되는 경우**

- key는 형제 요소들 사이에서 동일한 요소를 식별하는 값
- 리렌더링 최소화하려면 필수로 해야하는 작업이다.

```jsx
<li key={index}>{index}</li>
```

<br>
<br>

## <mark style='background-color: #ffdce0'>📌2. 렌더 단계</mark>

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <div>"트리거"되면 렌더 단계로 넘어가 DOM에 그려질 요소들을 파악하는 과정을 거치게 됩니다.</div>
</div>

- JSX 선언 또는 `React.createElement()`를 통해 일반 객체인 Reat 엘리먼트를 생성하는 단계
- 컴포넌트를 실행해 이 결과와 이전 가상 DOM을 비교하는 과정을 거쳐 변경이 필요한 컴포넌트를 체크하는 단계

<br>

1\. 컴포넌트의 루트에서부터 차근차근 아래쪽으로 내려가면서 업데이트가 필요하다고 지정돼 있는 모든 컴포넌트를 재귀적으로 찾는다.

<br>

2\. 업데이트가 필요한 컴포넌트를 발견하면 `FunctionComponent()` 그 자체를 호출한 뒤, 그 결과물을 저장한다.

형태 : JSX

```jsx
function Hello() {
  return (
    <TestComponent a={35} b="yceffor">
      안녕하세요
    </TestComponent>
  );
}
```

<br>

3\. JS가 컴파일 되고 배포 준비가 되는 시점에서 React.createElement를 호출해서 변환된다.

![Untitled (9)](https://github.com/chlwlstlf/data/assets/63334368/097cf58a-a6ba-4867-bfa1-aab9f7d81527)

React.createElement 호출

```jsx
function Hello() {
  return React.createElement(
    TestComponent,
    {a: {35} b: 'yceffor'},
    '안녕하세요',
  )
}
```

결과물은 브라우저의 UI 구조를 설명할 수 있는 일반적인 자바스크립트 객체를 반환

```jsx
{type: TestComponent, props: {a: {35} b: 'yceffor', children: "안녕하세요"}}
```

<br>

4\. React Element 객체 값들을 모아 가상 DOM을 생성한다.

![Untitled (10)](https://github.com/chlwlstlf/data/assets/63334368/2a5ae9fe-bc11-4c3c-a1fc-a7195e1ce2f8)

- 가상 DOM은 실제 DOM이 아니다.
- 값으로 표현된 UI이다.

<br>

5\. 업데이트가 발생했을 때 렌더 단계를 루트부터 다시 실행해서 새로운 가상 DOM을 만든다.

![Untitled (11)](https://github.com/chlwlstlf/data/assets/63334368/2c65dd9e-d7d4-490f-b309-bf0636e0b890)

<br>

6\. 리액트가 이전 렌더와 다음 렌더의 변화를 비교하는 과정을 재조정이라고 한다.

![Untitled (12)](https://github.com/chlwlstlf/data/assets/63334368/03fb68fd-dcd6-4038-8660-96d1bcab9c99)

업데이트가 많이 동시에 발생해도 액츄얼 DOM은 딱 한 번만 수정된다.

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <b>diffing 알고리즘</b>
  <p>실제 DOM과 Virtual DOM을 비교하여 변경된 부분만 실제 DOM에 반영하는 알고리즘
  </p>
  <div>- 컴포넌트 트리 비교</div>
  <div>- 요소 유형 확인(동일한 태그인지 비교)</div>
  <div>- Key 비교(동일한 key 요소끼리 비교)</div>
  <div>- 속성 비교(속성, 스타일 비교)</div>
  <div>- 재귀적으로 하위 노드 비교</div>
  <div>- 업데이트 전파(변경된 부분만 업데이트)</div>
</div>

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <b>동시성 렌더링</b>
  <p>- 의도된 우선순위로 컴포넌트를 렌더링해 최적화할 수 있는 비동기 렌더링(동시성 렌더링)이 리액트 18에서 도입됐다.
  </p>
  <p>- 동시성 렌더링은 렌더 단계가 비동기로 작동해 우선수위를 조정하거나 렌더링을 중단, 재시작, 포기한다.
  </p>
</div>

<br>
<br>

## <mark style='background-color: #ffdce0'>📌3. 커밋 단계</mark>

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <div>직전 렌더 단계에서 두 가상 DOM 트리 간 변화를 실제 DOM에 적용하는 단계입니다.</div>
</div>

1\. **실제 DOM 업데이트**

- 변경된 부분만을 실제 DOM에 반영하여 업데이트한다.
- 이 단계가 끝나면 브라우저의 렌더링이 발생한다.
- 이 과정에서는 DOM 조작이 수행되며, 화면에 변경된 내용이 실제로 표시된다.
  ![Untitled (13)](https://github.com/chlwlstlf/data/assets/63334368/60f1e32d-9401-4c3d-a5c5-dff744a755a6)

<br>

2\. **부가 작업 수행**

- 실제 DOM 업데이트 후에는 커밋 단계에서 추가적인 작업이 수행될 수 있다. 예를 들어, useEffect나 커스텀 DOM 이벤트 핸들러가 실행될 수 있다.

<br>

3\. **렌더링 완료**

- 모든 업데이트가 완료되면 렌더링 프로세스가 종료됩니다. 화면에는 최신의 UI가 표시됩니다.

<br>

4\. **중요❗**

- **리액트의 렌더링이 일어난다고 해서 무조건 DOM 업데이트가 일어나는 것은 아니다.**
- 렌더링을 수행했으나 커밋 단계까지 갈 필요가 없다면, 즉 변경 사항을 계산했는데 아무런 변경 사항이 감지되지 않는다면 이 커밋 단계는 생략될 수 있다.
- 커밋 단계가 생략되면 DOM 업데이트가 일어나지 않는다.

<br>
<br>

## <mark style='background-color: #ffdce0'>📌렌더링 프로세스 정리</mark>

리액트는 자제척인 렌더링 프로세스를 가지고 있다.

![Untitled (14)](https://github.com/chlwlstlf/data/assets/63334368/e0da86a0-d1cd-4b0f-9bb7-2940c17c2619)

이렇게 하는 이유?

- DOM의 수정을 최소화 하기 위해
- 대부분 상황에 충분히 빠른 업데이트를 보장하기 위해
- 항상 빠른 업데이트를 보장하지는 않는다(가상 DOM을 생성하고 비교하는 데도 연산이 소요되기 때문)

<br>
<br>

## <mark style='background-color: #ffdce0'>📌예제 코드</mark>

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

**B의 버튼을 눌렀을 때 렌더링 과정**

1. B 컴포넌트의 setState가 호출된다.
2. B 컴포넌트의 리렌더링 작업이 렌더링 큐에 들어간다.
3. 리액트는 트리 최상단에서부터 렌더링 경로를 검사한다.
4. A 컴포넌트는 리렌더링이 필요한 컴포넌트로 표시돼 있지 않으므로 별다른 작업을 하지 않는다.
5. 그다음 하위 컴포넌트인 B 컴포넌트는 업데이트가 필요하다고 체크돼 있으므로 B를 리렌더링한다.
6. 5번 과정에서는 B는 C를 반환했다.
7. C는 props인 number가 업데이트됐다. 그러므로 업데이트가 필요한 컴포넌트로 체크돼 있고 업데이트한다.
8. 7번 과정에서 C는 D를 반환했다.
9. D도 마찬가지로 업데이트가 필요한 컴포넌트로 체크되지 않았다. 그러나 C가 렌더링됐으므로 그 자식인 D도 렌더링됐다.

<br>
<br>

## <mark style='background-color: #ffdce0'>📌최적화: React.memo()</mark>

```jsx
const D = memo(() => {
  return <>리액트 재밌다!</>;
});
```

memo로 래핑되어 있으면 변경된 내용이 없기 때문에 렌더링이 생략되므로 커밋 단계도 생략됐다.

**HOW: props 동등 비교는 어떻게 이루어 질까?**

- props 혹은 props의 객체를 비교할 때 얕은(shallow) 비교를 한다.
- 서로 다른 2개 객체를 각각 모두 조사해서 내용물 중에 차이가 있는지 여부를 검사하는 것입니다.
- 이전 props와 현재 props가 같다면 true를 반환할 것이다.

---

**WHEN1: 언제 React.memo()를 써야할까?**

- 함수형 컴퍼넌트가 같은 `props`로 자주 렌더링 될거라 예상될 때이다.

**WHEN2: 언제 React.memo()를 사용하지 말아야 할까?**

- 렌더링될 때 `props`가 다른 경우가 대부분인 컴포넌트를 생각해보면, 메모이제이션 기법의 이점을 얻기 힘들다.
- 함수 객체는 "일반" 객체와 동일한 비교 원칙을 따른다. 함수 객체는 오직 자신에게만 동일하다.
- 콜백 함수는 동등 비교를 하면 항상 false를 뱉기 때문에 props에 콜백 함수가 있다면 계속 렌더링된다.

**정리**

- memo를 사용하면 리렌더링이 발생하지 않는다.
- 동일한 prop에 대해 리렌더링을 하는 것을 막을 수 있다.
- 다만, 콜백 함수를 prop으로 사용하는 컴퍼넌트에서 메모이징을 할 때 주의해야한다.

<br>
<br>

---

**참고자료**

[React.memo() 현명하게 사용하기](https://ui.toast.com/weekly-pick/ko_20190731)

[[10분 테코톡] 솔로스타의 React 렌더링](https://www.youtube.com/watch?v=eBDj0B0HbEQ)

[React.js의 렌더링 방식 살펴보기](https://www.youtube.com/watch?v=N7qlk_GQRJU)
