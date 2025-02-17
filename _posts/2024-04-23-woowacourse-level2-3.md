---
layout: single
title: "[우테코] Level2 Controlled & Uncontrolled Components, Hooks API, Router"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션1 LMS 강의 자료2

## <mark class="pink">📚Controlled & Uncontrolled Components</mark>

### <mark class="yellow">1. React에서 Input & Form 핸들링이 어려운 이유</mark>

React의 렌더링 방식과 전통적인 HTML 폼의 동작 방식이 다르기 때문이다.

<br>

**HTML 폼의 동작 방식**

기본 HTML에서 `<input>` 태그는 기본적으로 사용자가 직접 값을 입력하고 브라우저가 이를 관리한다. 즉, 입력값을 상태로 관리할 필요 없이, 단순히 `document.querySelector`로 값을 가져온다.

```html
<form>
  <input type="text" name="username" />
  <button type="submit">제출</button>
</form>
```

하지만 React에서는 컴포넌트가 UI를 상태(State)로 관리하기 때문에, 폼 입력값을 어떻게 다룰지에 대한 "제어 방식(Controlled vs Uncontrolled)" 을 고민해야 한다.

<br>
<br>

### <mark class="yellow">2. Controlled Component (제어 컴포넌트)</mark>

**[설명]**

React가 값(state)을 완전히 제어하는 요소이다. 즉, 사용자가 입력한 값이 React의 `state`에 저장되고, React의 상태가 바뀌면 UI가 업데이트되는 구조이다.

```ts
import { useState } from "react";

const ControlledInput = () => {
  const [value, setValue] = useState(""); // React state로 관리

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    setValue(e.target.value); // 사용자의 입력값을 state에 저장
  };

  return <input type="text" value={value} onChange={handleChange} />;
};
```

<br>

**[특징]**

1\. value 값이 항상 React state와 동기화된다.(`value={state}`)  
2\. React에서 직접 상태를 관리하므로, 단일 데이터 소스가 유지된다.  
3\. `onChange` 이벤트 핸들러를 사용해 입력값을 직접 업데이트해야 한다.

<br>

**[장점]**

1\. 폼 데이터를 쉽게 제어 가능 -> 상태 기반으로 값 조작  
2\. 동적 유효성 검사 가능 -> `value`를 기준으로 즉시 검증 가능  
3\. 사용자 입력을 추정하기 쉬움 -> `useState`가 언제든지 현재 값을 보장  
4\. 입력값을 포맷팅 가능 -> 대분자로 변환, 자동완성 등 가공된 값 적용가능

<br>

**[단점]**

1\. 매 입력마다 렌더링 발생 -> `setState`를 호출할 때마다 렌더링이 일어남  
2\. 입력 지연 가능성 -> 특히, 입력값을 처리하는 로직이 많아지면 성능 저하 가능  
3\. 입력 폼이 많을 경우 복잡해질 수 있음

<br>
<br>

### <mark class="yellow">3. Uncontrolled Component (비제어 컴포넌트)</mark>

**[설명]**

React가 `<input>`의 값을 직접 관리하지 않고, DOM 자체에서 입력을 처리하는 방식이다. 즉, React 상태(`state`)가 아니라, HTML의 `ref`를 통해 직접 가져오는 구조이다.

```ts
import { useRef } from "react";

const UncontrolledInput = () => {
  const inputRef = useRef<HTMLInputElement>(null);

  const handleSubmit = () => {
    console.log("입력값:", inputRef.current?.value); // 직접 DOM에서 값 가져옴
  };

  return (
    <>
      <input type="text" ref={inputRef} />
      <button onClick={handleSubmit}>제출</button>
    </>
  );
};
```

<br>

**[특징]**

1\. React state를 사용하지 않음 -> 입력값을 관리하지 않음 (`value` 속성 없음)  
2\. 사용자가 입력한 값을 직접 DOM에서 가져옴 -> `useRef`를 사용  
3\. 렌더링이 적음 -> 값이 변경될 때 `setState`를 호출하지 않기 때문

<br>

**[장점]**

1\. 렌더링이 적다 -> 값 변경 시 `setState`를 사용하지 않으므로 불필요한 렌더링이 발생하지 않음  
2\. 폼을 빠르게 처리할 수 있음 -> 대량의 입력 요소가 있을 때 더 효율적  
3\. 초기 값 설정이 가능 -> `defaultValue` 속성으로 기본값을 설정할 수 있음

```ts
const UncontrolledInput = () => {
  return <input type="text" defaultValue="기본값" />;
};
```

<br>

**[단점]**

1\. 입력값을 실시간으로 추적하기 어려움  
2\. 동적 유효성 검사 불가능 -> 입력이 끝난 후 `ref.current.value`를 확인해야 함  
3\. 상태 기반 UI 변경이 어려움

<br>
<br>

### <mark class="yellow">4. 생각해보기</mark>

**1\. 제어 컴포넌트를 지향하라는 의견이 많은 이유는 무엇일까요?**

React의 철학과 잘 맞고 유지보수성이 뛰어나기 때문이다.

1️⃣ React의 단방향 데이터 흐름과 일관성 유지  
React는 "단일 데이터 흐름"을 따르는 것이 강점이다. 제어 컴포넌트는 입력값이 항상 `state`에 저장되므로, 상태를 쉽게 추적할 수 있다. 따라서 어떤 컴포넌트에서는 동일한 데이터를 유지할 수 있다.

2️⃣ 동적 UI 핸들링이 쉬움  
입력값이 따라 실시간 검증, 자동 변환, 동적 렌더링 등을 쉽게 구현할 수 있다. 비제어 컴포넌트에서는 입력이 끝난 후 검사해야 해서 실시간 반영이 어렵다.

3️⃣ 유지보수성과 확장성이 뛰어남  
입력값이 `state`로 관리되므로, 테스트, 리팩토링, 기능 확장이 쉽다. `useState`, `useEffect`와 함께 사용하면 다양한 로직을 적용하기 용이하다. 하나의 `state`에서 여러 개의 입력값을 쉽게 관리할 수 있다.

4️⃣ Redux, Context API 등과 쉽게 연동 가능  
입력값을 전역 상태 관리 도구와 연동하기 쉽다. 상태를 전역에서 관리하면 다른 컴포넌트에서도 쉽게 접근 가능하다.

<br>

**2\. useImperativeHandle 이 언급되는 이유가 무엇일까요?**

`useImperativeHandle`은 비제어 컴포넌트에서 필요한 경우에 사용된다.

- 비제어 컴포넌트(ref를 사용)를 직접 조작해야 할 때
- 포커스 이동, 스크롤 조작, 애니메이션 트리거 등
- 서드파티 라이브러리와 통합할 때

```ts
import { useRef, useImperativeHandle, forwardRef } from "react";

const CustomInput = forwardRef((props, ref) => {
  const inputRef = useRef<HTMLInputElement>(null);

  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current?.focus(); // 부모 컴포넌트에서 `focus()` 호출 가능
    },
    getValue: () => inputRef.current?.value, // 입력값 가져오기
  }));

  return <input ref={inputRef} {...props} />;
});

const Parent = () => {
  const inputRef = useRef<{ focus: () => void; getValue: () => string }>(null);

  return (
    <>
      <CustomInput ref={inputRef} />
      <button onClick={() => inputRef.current?.focus()}>포커스</button>
      <button onClick={() => console.log(inputRef.current?.getValue())}>
        값 확인
      </button>
    </>
  );
};
```

useImperativeHandle을 사용하면 부모 컴포넌트에서 ref를 통해 하위 컴포넌트의 메서드를 직접 호출할 수 있다.  
Controlled Component에서는 `state`를 변경하면 되지만, DOM을 직접 조작해야 하는 경우 `useImperativeHandle`이 필요할 수도 있다.

<br>

**3\. 비제어 컴포넌트는 사용할 일이 없는건가요?**

비제어 컴포넌트도 특정한 경우에 유용하게 사용할 수 있다.

1\. 폼 데이터가 많을 때 (렌더링 최적화)  
입력 필드가 많을 경우, `state`로 관리하면 매 입력마다 렌더링이 발생할 수 있다. `useRef`를 사용하면 렌더링을 최소화하면서 입력을 관리할 수 있다.

2\. 기본값이 있는 입력 필드 (초기값 유지 필요)  
`<input defaultValue="기본값" />` 처럼 기본값이 유지되면서 값이 변경되는 경우에 사용한다.

3\. React 외부에서 값이 변경되는 경우  
예를 들어, 서드파트 라이브러리(파일 업로드)와 함께 사용할 때 React에서 제어하기 어려운 경우가 많다.

4\. 빠르게 값만 가져오면 되는 경우  
`useRef`를 사용하면 렌더링 없이 즉시 값을 가져올 수 있다.

<br>
<br>

## <mark class="pink">📚Hooks API</mark>

### <mark class="yellow">1. React 과거의 문제</mark>

- 재사용성에 대한 문제: class 기반에서는 로직을 재사용하는 방법이 복잡하게 느껴졌다.
- 중복과 관심사 분리에 대한 문제: 라이프사이클 메서드에 다양한 관심사의 로직이 조각조각 흩어지는 일이 자주 발생했다.
- 개념 자체로 인한 문제: this 바인딩을 이해하기 어렵다.

<br>
<br>

### <mark class="yellow">2. Hooks API</mark>

**Hook이란?**

Function Component에서도 React가 제공하는 모든 기능들을 가져다(hook into) 쓸 수 있게 해주는 React가 제공해주는 함수

<br>
<br>

### <mark class="yellow">3. useState</mark>

상태를 사용할 수 있게 해주는 훅이다.  
set function에는 위의 예시 코드처럼 새로운 상태값을 바로 넘겨줄 수도 있고, 함수를 넘겨줄 수도 있다.

**새로운 상태값을 직접 전달**

```jsx
const [state, setState] = useState(initialState);

setState(newState);
```

count가 1만 증가한다. `setCount(count + 1)`을 호출할 때마다, `count`의 값은 여전히 "이전 렌더에서의 값"을 사용하기 때문이다.  
이 문제를 해결하려면 업데이트 함수(updater function) 을 사용해야 한다.

```jsx
const increaseMultiple = () => {
  setCount(count + 1);
  setCount(count + 1);
  setCount(count + 1);
};
```

<br>

**업데이트 함수를 전달**

set function에 함수를 넘겨주면 해당 함수는 updater function으로 동작한다.

업데이트 함수(`prevState => newState`)를 전달하면 이전 상태값을 보장한다.  
prevCount는 항상 최신 값이므로, increaseCorrectly를 호출하면 정확히 3 증가한다.

```jsx
const increaseCorrectly = () => {
  setCount((prevCount) => prevCount + 1);
  setCount((prevCount) => prevCount + 1);
  setCount((prevCount) => prevCount + 1);
};
```

<br>
<br>

### <mark class="yellow">4. useEffect</mark>

외부 시스템과 컴포넌트를 동기화 해주기 위해 사용하는 리액트 훅이다.

'컴포넌트가 mount될 때', '컴포넌트가 매번 render될 때', '컴포넌트가 unmount될 때', '컴포넌트가 의존하고 있는 특정 값이 업데이트될 때' 등의 시점을 지정해 외부 시스템과 컴포넌트를 동기화하는 작업을 수행할 수 있다.

```jsx
useEffect(() => {
  // render 이후 업데이트

  return () => {
    // cleanup - unmount
  };
}, [dependencies]);
```

- 매 render가 끝난 이후 첫 번째 인자로 주어진 함수 호출
- unmount 될 때 뭔가 처리해주어야 한다면 함수를 리턴
- 특정 값이 업데이트 될 때만 호출하길 원한다면 두 번째 인자로 해당값 배열로 지정

<br>

💡 Effect를 쓰지 않고 해결할 수 있다.

useEffect는 컴포넌트를 '외부 시스템과 동기화'하기 위한 훅이다.  
이는 외부 시스템과 동기화하는 작업이 아니라면 useEffect 훅을 쓰지 않아도 된다는 말이다.

<br>
<br>

### <mark class="yellow">5. 생각해보기</mark>

**1\. Hooks를 사용할 때 반드시 지켜야 하는 사용 규칙은 무엇인가요?**

1️⃣ Hooks는 최상위에서만 호출해야 한다.

if, for while, function 등의 블록 내부에서 Hook을 호출하면 안 된다.  
React는 Hook이 호출되는 순서에 따라 내부적으로 상태를 저장한다. 조건문 안에서 Hook을 사용하면, 렌더링이 달라질 때 순서가 꼬이면서 예상치 못 한 동작이 발생할 수 있다.

<br>

2️⃣ Hooks는 React 함수 컴포넌트 또는 Custom Hook 안에서만 호출해야 한다.

일반 함수나 클래스 컴포넌트에서는 훅은 사용할 수 없다.  
React는 Hooks를 "컴포넌트의 상태 관리 도구"로 설계했기 때문에, 일반 함수에서는 사용할 수 없다. 커스텀 훅을 만들면 재사용이 가능하고, 규칙을 준수하면서도 로직을 분리할 수 있다.

<br>

3️⃣ useEffect에서 deps를 올바르게 관리해야 한다.

useEffect를 사용할 때 의존성 배열을 잘못 관리하면 무한 루프가 발생할 수 있다.

<br>
<br>

**2\. '함수'가 어떻게 '상태'를 가질 수 있는 걸까요?**

1️⃣ useState 동작 원리

React에서 함수형 컴포넌트는 매 렌더링마다 새로운 함수가 실행된다.  
하지만 useState가 호출된 위치를 기억해서 이전 상태를 유지할 수 있다.

즉, "상태"를 "함수"가 직접 가지는 게 아니라, React가 이를 유지하면서 매 렌더링 때 이전 값을 돌려주는 것이다.

<br>

2️⃣ React가 상태를 저장하는 방법

React는 컴포넌트가 렌더링될 때 각 Hook을 호출한 순서대로 저장하는 배열(React Fiber 내부 메모리)을 유지한다.

1\. `useState(0)`을 처음 호출하면, React는 상태 배열의 첫 번째 자리에 `0`을 저장한다.  
2\. `setCount(1)`을 호출하면, React는 상태 배열을 업데이트하고 다음 렌더링 때 `1`을 반환한다.  
3\. 컴포넌트가 다시 렌더링되면, React는 이전 렌더링에서 사용된 `useState`의 순서를 참고해 이전 값을 반환한다.

<br>
<br>

## <mark class="pink">📚Router</mark>

### <mark class="yellow">1. 라우팅(Routing)이란 무엇인가</mark>

웹 어플리케이션을 개발하는 관점에서 라우팅이란 특정 경로로 들어오는 요청을 어떻게 처리할 지를 결정하는 방식이라 할 수 있다.

가장 전통적인 방식의 웹 어플리케이션에서는 아래와 같이 링크를 만들어 다른 페이지로 이동할 수 있었다.

```html
<nav>
  <ul>
    <li><a href="/">abc</a></li>
    <li><a href="/a">a</a></li>
    <li><a href="/b">b</a></li>
    <li><a href="/c">c</a></li>
  </ul>
</nav>
```

각각의 링크를 클릭하면, 해당 경로로 서버에 요청을 보내고 응답을 받아 화면을 다시 그리는 식으로 동작하게 된다.

```js
// app.METHOD(PATH, HANDLER)

app.get("/", function (req, res) {
  res.render("index");
});

app.get("/a", function (req, res) {
  res.render("a");
});

app.get("/b", function (req, res) {
  res.render("b");
});

app.get("/c", function (req, res) {
  res.render("c");
});
```

<br>
<br>

### <mark class="yellow">2. 클라이언트 사이드 라우팅(Client-side Routing)</mark>

원래 라우팅은 서버에서 해주던 일이었다. 그런데 SPA가 등장하면서 클라이언트에서 자체적인 라우팅 시스템을 구성해 사용하는 방법이 같이 사용되기 시작했다.

SPA에서 라우팅을 사용하는 경우, 처음 어플리케이션에 진입할 때 받은 리소스들에 라우팅 작업을 처리해줄 수 있는 클라이언트 라우터가 포함되어 있다. 이후 사용자가 경로를 이동하는 등의 작업을 하게 되면, 서버로 요청을 보내는 것이 아니라 이 클라이언트 라우터에서 처리를 대신 해주는 것이다.

<br>
<br>

### <mark class="yellow">3. 라우팅을 위해 필요한 것들</mark>

**[필요한 작업]**

1\. 실제로 요청 들어오는 URI를 분석하기  
2\. 어떤 경로로 들어왔을 때 어떤 걸 보여줄 지 연결하기  
3\. 경로가 변경되는 history를 관리하기

<br>

**[URI의 구성]**

![](https://techcourse-storage.s3.ap-northeast-2.amazonaws.com/77632925d0b4496cad0a070b14224896)

<br>
<br>

### <mark class="yellow">4. 구현체: react-router</mark>

[react-router](https://reactrouter.com/)는 이런 클라이언트 라우팅을 사용하기 쉽게 구현한 서드파티 라이브러리 중 하나이다. 'react-'가 붙어있어 마치 공식같지만 아직까지 리액트에서 공식적으로 제공하고 있는 자체적인 라우팅 시스템은 없다.

1\. 설치하기

```bash
npm install react-router-dom
// or
yarn add react-router-dom
```

<br>

2\. 라우터 코드 작성하기

```tsx
<Router>
  <Route exact path="/">
    <Home />
  </Route>
  <Route path="/a">
    <A />
  </Route>
  <Route path="/b">
    <B />
  </Route>
  <Route path="/c">
    <C />
  </Route>
</Router>
```

<br>

3\. 사용하기

```tsx
import { BrowserRouter as Router, Switch, Route, Link } from "react-router-dom";

<Router>
  <ul>
    <li>
      <Link to="/">알파벳</Link>
    </li>
    <li>
      <Link to="/a">에이</Link>
    </li>
    <li>
      <Link to="/b">비</Link>
    </li>
    <li>
      <Link to="/c">씨</Link>
    </li>
  </ul>
</Router>;
```

<br>
<br>

### <mark class="yellow">5. 생각해보기</mark>

**1\. 서버 사이드 라우팅으로 할 수 없는, 클라이언트 사이드 라우팅의 장점이 있을까요?**

1️⃣ 빠른 페이지 전환 (클라이언트에서 즉시 처리)

SSR: 새로운 페이지 요청마다 서버에서 HTML을 새로 렌더링해서 반환해야 한다. -> 페이지 전환 속도 느림  
CSR: 한 번 로드된 후에는 클라이언트에서 URL을 변경하면서 필요한 데이터만 가져온다. -> 빠른 페이지 전환 가능

`a href="/about"`을 사용하면 서버 요청이 발생하지만,  
`Link to="/about"`을 사용하면 새로고침 없이 즉시 이동 → UX가 훨씬 부드러움

<br>

2️⃣ 서버 부하 감소 (필요한 데이터만 요청)

SSR: 매번 서버가 새로운 HTML을 생성해야 하므로 서버 부하 증가  
CSR: 초기 로딩 이후에는 클라이언트가 필요한 데이터만 API 요청 → 서버 부하 감소

페이지를 이동할 때마다 전체 HTML을 다시 로드하는 것이 아니라, JSON 데이터만 요청하면 된다.

<br>

3️⃣ 애니메이션 & 트랜지션 활용 가능 (SPA 특화)

SSR: 새 페이지를 요청할 때 화면이 깜빡이면서 변경됨 → 부드러운 전환이 어려움  
CSR: 클라이언트에서 애니메이션과 전환 효과를 자연스럽게 적용 가능

<br>

4️⃣ 오프라인 지원 가능 (PWA & 캐싱)

SSR: 서버 요청이 필요하기 때문에 오프라인에서는 페이지 로딩 불가  
CSR: 서비스 워커(Service Worker) & 캐싱을 활용해 오프라인에서도 일부 기능 제공 가능

```tsx
navigator.serviceWorker.register("/service-worker.js");
```

<br>

5️⃣ 더 유연한 동적 라우팅 가능

SSR: 페이지를 렌더링할 때만 URL을 해석하여 페이지를 제공함  
CSR: 라우트 변경을 감지하여 동적으로 컴포넌트를 변경 가능

CSR에서는 클라이언트 측에서 URL을 해석하여 필요한 데이터만 가져올 수 있다.

<br>
<br>

**2\. 클라이언트 사이드에서 라우팅을 적용할 때, 대응해야 하는 케이스에는 어떤 것들이 있을까요?**

1️⃣ 페이지 새로고침 시 404 오류 (서버 설정 필요)

CSR에서는 실제 HTML 파일이 존재하지 않는다. `/about`을 직접 새로고침하면, 서버가 `/about` 페이지를 찾기 못해 404 오류가 발생한다.

서버에서 모든 요청을 `index.html`로 리다이렉션하도록 설정해야 한다.

<br>

2️⃣ SEO 문제 (검색 엔진 최적화 부족)

CSR은 기본적으로 JavaScript가 실행된 후에 콘텐츠가 렌더링된다. 일부 검색 엔진은 JavaScript를 실행하지 않기 때문에, 빈 화면이 노출될 수 있다.

SSR 또는 SSG를 활용하여 미리 HTML을 생성한다. 메타 태그(OG 태그) 설정으로 SNS 미리보기를 지원한다.

```html
<meta property="og:title" content="코레아-CoReA(Code Review Area)" />
<meta
  property="og:description"
  content="주니어 개발자들이 서로 코드리뷰하고 피드백 받을 수 있는 플랫폼"
/>
<meta property="og:image" content="./logo.png" />
<meta property="og:url" content="https://code-review-area.com" />
<meta property="og:type" content="website" />
<meta property="og:locale" content="ko_KR" />
```

<br>

3️⃣ 첫 페이지 로딩 속도 문제 (JavaScript 로드 필요)

SSR은 서버에서 HTML을 바로 제공하지만, CSR은 초기 로딩 시 JavaScript가 다운로드된 후 렌더링이 시작된다. → 첫 화면이 늦게 보일 수 있음

코드 스플리팅(Code Splitting) & Lazy Loading 활용하여 필요한 페이지만 JavaScript로 로드하여 성능을 개선한다.

```tsx
const About = React.lazy(() => import("./About"));

<Suspense fallback={<div>로딩 중...</div>}>
  <About />
</Suspense>;
```

<br>

4️⃣ 브라우저 뒤로 가기(Back) 대응

SPA에서는 브라우저의 "뒤로 가기" 기능이 기존 페이지가 아닌 새로운 상태를 유지해야 한다. URL만 변경되고 상태가 유지되지 않으면 이전 데이터를 다시 불러오는 문제가 발생할 수 있다.

React Router의 useHistory 또는 useNavigate를 사용하여 상태 관리한다.

```tsx
import { useNavigate } from "react-router-dom";

const Page = () => {
  const navigate = useNavigate();

  return (
    <>
      <button onClick={() => navigate(-1)}>뒤로 가기</button>
    </>
  );
};
```
