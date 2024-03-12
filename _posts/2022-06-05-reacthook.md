---
layout: single
title: "react hooks"
categories: React
toc: true
toc_sticky: true
---

# react 라이브러리 알아보기

※ 꼭 알아야하는 것

- 함수 컴포넌트는 함수다.
- 함수 컴포넌트는 상태 변화 시 다시 렌더링이 된다.
- 렌더링 시, 함수 내부 변수는 초기화가 된다.
- 함수는 객체다.
- 초기화가 된다는 건 새로운 객체가 다시 만들어지고, 메모리 주소도 새로 생성 된다.

```jsx
import React, {
  useState,
  useEffect,
  useContext,
  useRef,
  useReducer,
  useCallback,
  useMemo,
  useLayoutEffect,
  useImperativeHandle,
  useDebugValue,
} from "react";
```

## <mark style='background-color: #ffdce0'>0. React</mark>

```jsx
import React from "react";
```

- 브라우저가 읽을 수 있도록 jsx를 바꾸기 위해 작성
- 상위 컴포넌트에 작성되었다면 하위에서는 생략 가능
- React 17버전부터 작성하지 않아도 된다.

---

# 기본 훅 (3개)

## <mark style='background-color: #ffdce0'>1. useState</mark>

- 구성 요소 <mark style='background-color: #fff5b1'>상태를 관리</mark>하는 데 사용된다.
- 현재 상태 값과 해당 상태를 업데이트하는 함수가 포함된 배열을 반환한다.

```jsx
import React, { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  const increment = () => setCount(count + 1);
  const decrement = () => setCount(count - 1);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>Increment</button>
      <button onClick={decrement}>Decrement</button>
    </div>
  );
}
```

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>count는 현재 상태 값, setCount는 해당 상태를 업데이트하는 함수</div>
    <div>useState(0)에서 0은 초기 값(null, [] 등 가능)</div>
    <div>setCount(count + 1)는 count값을 하나 증가하여 count에 저장</div>
  </p>
</div>

---

## <mark style='background-color: #ffdce0'>2. useEffect</mark>

- 데이터 가져오기, 구독, DOM 조작과 같은 구성 요소의 <mark style='background-color: #fff5b1'>특정 상태를 관리</mark>하는 데 사용된다.
- 효과가 실행되는 시점을 제어하기 위해 함수와 선택적 종속성 배열을 허용한다.

```jsx
import React, { useEffect, useState } from "react";

function OnceEffectExample() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log("효과가 한 번 실행되었습니다");
  }, []);

  const increment = () => {
    setCount(count + 1);
  };

  return (
    <div>
      <p>카운트: {count}</p>
      <button onClick={increment}>증가</button>
    </div>
  );
}

export default OnceEffectExample;
```

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>useEffect 마지막에 []가 빈 배열이면 처음 화면이 렌더링 될 때만 useEffect 안의 코드를 실행시킨다.</div>
    <div>[count]로 바꾸게 되면 count 값이 바뀔 때마다 코드가 실행된다.</div>
  </p>
</div>

---

## <mark style='background-color: #ffdce0'>3. useContext</mark>

- 기능 구성요소의 <mark style='background-color: #fff5b1'>컨텍스트 값</mark>에 액세스하는 데 사용된다.
- 구성 요소가 컨텍스트 변경 사항을 구독하고 컨텍스트 값에 액세스할 수 있도록 한다.

```jsx
import React, { useContext, createContext } from "react";

// 1. 컨텍스트 생성
const ThemeContext = React.createContext();

// 2. 컨텍스트 프로바이더 생성
function App() {
  const theme = "light";

  return (
    <ThemeContext.Provider value={theme}>
      <Header />
      <Main />
    </ThemeContext.Provider>
  );
}

// 3. useContext를 사용하여 컨텍스트 값에 접근
function Header() {
  const theme = useContext(ThemeContext);

  return <header>테마: {theme}</header>;
}

function Main() {
  const theme = useContext(ThemeContext);

  return <main>테마: {theme}</main>;
}

export default App;
```

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>1. `ThemeContext`라는 컨텍스트를 생성(공유하려는 데이터를 정의하는 역할)</div>
    <div>2. `App` 컴포넌트에서 `ThemeContext.Provider`를 사용하여 컨텍스트 값을 제공한다. 이 컴포넌트 내에서 자식 컴포넌트들은 theme 값을 공유할 수 있다.</div>
    <div>3. `Header`와 `Main` 컴포넌트에서 `useContext(ThemeContext)`를 사용하여 컨텍스트 값을 읽어온다. 이렇게 함으로써 두 컴포넌트는 theme 값을 공유하고 사용할 수 있다.</div>
  </p>
  <p>
    <div>props vs useContext</div>
    <div>props는 부모-자식 관계만 데이터 전달이 가능하다. useContext는 props을 거치지 않기를 원할 때 사용한다.</div>
  </p>
  <p>
    <div>Recoil vs useContext</div>
    <div>useContext는 주로 간단한 상태 관리 및 컨텍스트 공유를 위해 사용되는 반면, Recoil은 더 복잡한 상태 관리 시나리오에 적합하며 성능 최적화와 더 넓은 기능 집합을 제공한다.</div>
  </p>
</div>

---

# 추가 훅(7개)

## <mark style='background-color: #ffdce0'>4. useRef</mark>

- 렌더링 전반에 걸쳐 지속되는 <mark style='background-color: #fff5b1'>DOM 요소</mark> 또는 값에 대한 변경 가능한 참조를 생성하는 데 사용된다.
- 참조된 값에 접근하기 위한 .current 속성을 제공한다.

```jsx
import React, { useRef, useEffect } from "react";

function InputFocusExample() {
  // 1. 접근할 ref를 생성
  const inputRef = useRef(null);

  // 2. 입력 요소에 자동으로 포커스
  useEffect(() => {
    inputRef.current.focus();
  }, []);

  return (
    <div>
      <h1>useRef Example</h1>
      <input type="text" ref={inputRef} />
      <p>Start typing here:</p>
    </div>
  );
}

export default InputFocusExample;
```

```jsx
useEffect(() => {
  if (storeListRef.current && checkedStore) {
    const index = searchResult.findIndex((store) => store.id === checkedStore);
    storeListRef.current.scrollTo({
      top: index * 106,
      behavior: "smooth",
    });
  }
}, [checkedStore, searchResult]);
```

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>inputRef.current.focus();는 input에 focus 시킴</div>
    <div>storeListRef.current.scrollTo에는 top과 behavior를 지정할 수 있으며, top은 스크롤을 어디까지 내릴건지 지정하는 것이고, behavior은 스크롤 움직임을 어떻게 할 건지 지정하는 것이다.</div>
  </p>
</div>

## <mark style='background-color: #ffdce0'>5. useReducer</mark>

- 보다 복잡한 상태 로직을 관리하기 위한 <mark style='background-color: #fff5b1'>useState의 대안</mark>이다.
- 리듀서 함수와 초기 상태를 취하고 현재 상태와 디스패치 함수를 반환한다.

```jsx
import React, { useReducer } from "react";

// 리듀서 함수
const counterReducer = (state, action) => {
  switch (action.type) {
    case "INCREMENT":
      return { count: state.count + 1 };
    case "DECREMENT":
      return { count: state.count - 1 };
    default:
      return state;
  }
};

function Counter() {
  // useReducer를 사용하여 상태 초기화
  const [state, dispatch] = useReducer(counterReducer, { count: 0 });

  return (
    <div>
      <h1>카운터: {state.count}</h1>
      <button onClick={() => dispatch({ type: "INCREMENT" })}>증가</button>
      <button onClick={() => dispatch({ type: "DECREMENT" })}>감소</button>
    </div>
  );
}

export default Counter;
```

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>useReducer에 첫 번째 인자로 counterReducer를, 두 번째 인자로 초기 상태인 { count: 0 }를 전달한다.</div>
    <div>addTodo 함수와 removeTodo 함수를 통해 상태를 변경한다.</div>
  </p>
  <div>※ 초깃값이 object 형식일 때 편리</div>
</div>

---

## <mark style='background-color: #ffdce0'>6. useCallback</mark>

- <mark style='background-color: #fff5b1'>함수를 메모</mark>하고 하위 구성 요소가 불필요하게 다시 렌더링되는 것을 방지하는 데 사용된다.
- 함수와 종속성 배열이 필요하다.

```jsx
import React, { useState, useCallback } from "react";

function Button({ onClick }) {
  console.log("Button rendered");
  return <button onClick={onClick}>클릭하세요</button>;
}

function App() {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => {
    setCount(count + 1);
  }, [count]);

  return (
    <div>
      <h1>카운트: {count}</h1>
      <Button onClick={handleClick} />
    </div>
  );
}

export default App;
```

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>App 컴포넌트에서 useState를 사용하여 count 상태를 관리한다.</div>
    <div>useCallback을 사용하여 handleClick 함수를 정의한다. 의존성으로 count를 지정하여, count가 변경될 때만 함수가 다시 생성되도록 한다.</div>
  </p>
</div>

## <mark style='background-color: #ffdce0'>7. useMemo</mark>

- <mark style='background-color: #fff5b1'>계산된 값을 메모</mark>하고 불필요한 재계산을 방지하는 데 사용된다.
- 값과 종속성 배열을 계산하는 함수가 필요하다.

```jsx
import React, { useState, useMemo } from "react";

function FactorialCalculator({ number }) {
  const factorial = useMemo(() => {
    console.log("Calculating factorial...");
    let result = 1;
    for (let i = 1; i <= number; i++) {
      result *= i;
    }
    return result;
  }, [number]);

  return (
    <div>
      <p>
        Factorial of {number} is: {factorial}
      </p>
    </div>
  );
}

function App() {
  const [inputNumber, setInputNumber] = useState(5);

  return (
    <div>
      <h1>Factorial Calculator</h1>
      <input
        type="number"
        value={inputNumber}
        onChange={(e) => setInputNumber(Number(e.target.value))}
      />
      <FactorialCalculator number={inputNumber} />
    </div>
  );
}

export default App;
```

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>useMemo를 사용하면 팩토리얼 계산이 메모이제이션되어, number 입력이 변경될 때만 재계산된다. 이를 통해 불필요한 계산을 방지하고 컴포넌트의 성능을 향상시킬 수 있다.</div>
  </p>
</div>

## <mark style='background-color: #ffdce0'>8. useLayoutEffect</mark>

- useEffect와 유사하지만 모든 DOM 변형 후에 <mark style='background-color: #fff5b1'>동기적</mark>으로 실행된다.
- 거의 필요하지 않으며 종종 useEffect로 충분하다.

## <mark style='background-color: #ffdce0'>9. useImperativeHandle</mark>

- React.forwardRef 사용 시 노출되는 인스턴스 값을 커스터마이징하는 데 사용된다.

## <mark style='background-color: #ffdce0'>10. useDebugValue</mark>

- <mark style='background-color: #fff5b1'>사용자 정의 후크 디버깅</mark>에 사용된다.
- React DevTools의 사용자 정의 후크에 대한 레이블을 제공할 수 있다.
