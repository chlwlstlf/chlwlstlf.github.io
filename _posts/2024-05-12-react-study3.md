---
layout: single
title: "[React]useRef, useEffect"
categories: react
toc: true
toc_sticky: true
---

# 리액트 스터디3 - useRef, useEffect

## <mark class="pink">1. useEffect란?</mark>

**1\. useEffect의 목적**

- 부수 효과 관리: useEffect를 사용하여 컴포넌트에서 발생하는 부수 효과를 관리할 수 있다. 이는 데이터 가져오기, 구독 설정, DOM 조작, 타이머 설정 등과 같은 작업을 포함한다.

- 컴포넌트 생명주기 연동: useEffect를 사용하여 컴포넌트의 생명주기와 관련된 작업을 수행할 수 있다. 예를 들어, 컴포넌트가 마운트될 때, 업데이트될 때, 혹은 언마운트될 때 특정 작업을 실행할 수 있다.

- 상태와 props 감시: useEffect를 사용하여 특정 상태나 props의 변화를 감지하고, 이에 따라 특정 동작을 수행할 수 있다. 이를 통해 상태나 props의 변경에 따른 반응형 UI를 구현할 수 있다.

<br>
<br>

**2\. useEffect의 이점**

- 코드 분리와 모듈화: 부수 효과 관련 로직을 useEffect 안에 넣음으로써, 컴포넌트의 코드를 간결하게 유지하고 부수 효과 관련 로직을 모듈화할 수 있다.

- 생명주기 관리의 용이성: useEffect를 사용하여 컴포넌트의 생명주기와 관련된 작업을 효율적으로 관리할 수 있다. 이는 코드의 가독성을 높이고, 컴포넌트의 동작을 예측 가능하게 만든다.

- 성능 최적화: useEffect를 사용하여 필요한 시점에만 부수 효과를 실행하도록 설정함으로써, 불필요한 작업을 피하고 애플리케이션의 성능을 최적화할 수 있다.

<br>
<br>

**3\. useEffect 추가 설명**

- 비동기: setter 함수를 호출 한 즉시에는 useEffect의 로직 함수가 호출되지는 않는다.
- 동기: useEffect가 여러개 작성되어 있는 경우에는 제일 먼저 작성 되어 있는 useEffect 부터 제일 마지막에 작성되어 있는 useEffect 순으로 순서대로 호출된다.

<br>
<br>

## <mark class="pink">2. useEffect의 동작 원리</mark>

**1\. 컴포넌트의 생명주기**

- 화면에 추가될 때 `마운트`
- 새로운 props나 state를 수신하면 `업데이트`
- 화면에서 제거되면 `언마운트`

<br>

**2\. Effect의 생명주기**

- useEffect는 React 컴포넌트의 렌더링 주기 중 특정 시점에 실행되는 함수
- useEffect는 컴포넌트가 마운트될 때, 업데이트될 때, 혹은 언마운트될 때 실행되는데, 이때마다 부수 효과를 처리할 수 있다.
- useEffect는 기본적으로 컴포넌트가 마운트될 때와 업데이트될 때마다 실행된다.
- 이때, useEffect의 의존성 배열을 통해 특정 상태나 props의 변화를 감지하여 실행 여부를 결정할 수 있다.
- useEffect의 의존성 배열에 명시된 값들이 변경될 때에만 useEffect가 다시 실행된다.

```jsx
import { useState, useEffect } from "react";

function ComponentLifecycleExample(): JSX.Element {
  // 마운트: 1번째 실행 (콘솔 출력, state 초기화)
  console.log("컴포넌트 내부 코드를 실행합니다.");
  const [count, setCount] = useState < number > 0;

  useEffect(() => {
    // 마운트 및 업데이트: 3번째 실행
    console.log("컴포넌트가 렌더링 또는 업데이트 되었습니다.");
    return () => {
      // 언마운트: 4번째 실행(클린업 함수)
      console.log("컴포넌트가 언마운트되기 전에 호출됩니다.");
    };
  }, [count]);

  return (
    <>
      {/* 2번째 실행 (렌더링) */}
      <p>Count: {count}</p>
      <button
        onClick={() =>
          setCount((prev) => {
            return prev + 1;
          })
        }
      >
        증가
      </button>
    </>
  );
}

export default ComponentLifecycleExample;
```

<br>
<br>

## <mark class="pink">3. useEffect의 사용법</mark>

<mark class="yellow">Effect</mark>

- 특정 이벤트가 아닌 렌더링에 의해 직접 발생한다.
- 서버 연결 설정은 어떤 상호 작용과도 상관없이 발생해야 하므로 Effect이다.
- 커밋 단계가 끝난 후 화면 업데이트가 이루어지고 나서 실행된다.
- 이 시점이 React 컴포넌트를 외부 시스템과 동기화하기 좋은 타이밍이다.
- 이 안에는 setter함수가 아닌 순수 함수만 있어야한다.

<br>

<mark class="yellow">의존성 배열</mark>

- 의존성 배열이 없는 경우

  ```jsx
  useEffect(() => {
    // 모든 렌더링 후에 실행
  });
  ```

- 의존성 배열이 비어있는 경우

  ```jsx
  useEffect(() => {
    // 마운트될 때만 실행 (컴포넌트가 나타날 때)
  }, []);
  ```

- 의존성 배열이 있는 경우
  ```jsx
  useEffect(() => {
    // 마운트될 때 실행
    // 렌더링 이후에 a 또는 b 중 하나라도 변경된 경우에도 실행
  }, [a, b]);
  ```

<br>
<br>

## <mark class="pink">4. useEffect의 활용</mark>

- 데이터 가져오기
- 구독 설정
- 이벤트 처리

```jsx
import React, { useState, useEffect } from "react";

function ExampleComponent() {
  const [data, setData] = useState(null);

  // 데이터 가져오기
  useEffect(() => {
    fetchData(); // 데이터 가져오기 함수 호출
  }, []);

  // 구독 설정
  useEffect(() => {
    const subscription = subscribeToData((data) => {
      setData(data);
    });

    return () => {
      subscription.unsubscribe(); // 구독 해제
    };
  }, []);

  // 이벤트 처리
  useEffect(() => {
    const handleClick = () => {
      console.log("Button clicked!");
    };

    document.addEventListener("click", handleClick);

    return () => {
      document.removeEventListener("click", handleClick); // 이벤트 리스너 제거
    };
  }, []);

  // 타이머 설정
  useEffect(() => {
    const timer = setTimeout(() => {
      console.log("Timer expired!");
    }, 5000);

    return () => {
      clearTimeout(timer); // 타이머 해제
    };
  }, []);

  return (
    <div>
      <h2>Example Component</h2>
      {/* 데이터 표시 및 이벤트 핸들링 */}
      <button onClick={() => fetchData()}>Fetch Data</button>
      {data && <p>Data: {JSON.stringify(data)}</p>}
    </div>
  );
}

export default ExampleComponent;
```

<br>
<br>

## <mark class="pink">5. useEffect 주의할 점</mark>

**1\. Effect가 두 번 실행되는 경우**

- Strict Mode를 켜두면 개발 환경에서만 컴포넌트를 다시 마운트하여 두 번 실행된다.
- 첫 번째 연결이 종료되지 않은 채 두 번째 연결이 설정되었기 때문이다.
- Strict Mode는 켜두고 Effect 함수 내에서 클린업 함수를 구현하는 것을 권장한다.
- 클린업 함수를 사용하면 메모리 누수를 해결할 수 있다.

<mark class="yellow">클린업 함수 예시</mark>

```jsx
useEffect(() => {
  const connection = createConnection();
  connection.connect();
  return () => connection.disconnect(); //클린업 함수
}, []);
```

<br>
<br>

**2\. Effect가 필요하지 않은 경우**

- 렌더링 중에 무언가를 계산할 수 있을 때
- props나 state로 계산 가능할 때
- 이벤트 핸들러에 함수 추가할 때

<br>
<br>

<mark class="yellow">1) 렌더링을 위해 데이터를 변환할 때</mark>

- 컴포넌트의 최상위 레벨에서 모든 데이터를 변환한다.
- 그러면 props나 state가 변경될 때마다 해당 코드가 자동으로 다시 실행된다.

<br>

<mark class="yellow">틀린 코드</mark>

```jsx
function Form() {
  const [firstName, setFirstName] = useState("Taylor");
  const [lastName, setLastName] = useState("Swift");

  // 🔴 중복된 state 및 불필요한 Effect
  const [fullName, setFullName] = useState("");
  useEffect(() => {
    setFullName(firstName + " " + lastName);
  }, [firstName, lastName]);
  // ...
}
```

<br>

<mark class="yellow">올바른 코드</mark>

```jsx
function Form() {
  const [firstName, setFirstName] = useState("Taylor");
  const [lastName, setLastName] = useState("Swift");
  // 렌더링 중에 계산됨
  const fullName = firstName + " " + lastName;
  // ...
}
```

<br>
<br>

<mark class="yellow">2) 사용자 이벤트를 핸들링할 때</mark>

- 사용자가 제품을 장바구니에 넣을 때마다 알림을 표시하고 싶다.
- useEffect로 코드 중복을 제거해야할까?
- 핸들러 함수에서 매번 해당 함수를 호출해야할까?
- 후자가 맞다.

<br>

- 이벤트 핸들러는 버튼 클릭과 같이 항상 “수동으로” 트리거 되지만,
- Effect는 동기화 유지에 필요한 만큼 자주 실행 및 재실행되기 때문에 “자동으로” 트리거 된다.

<br>

<mark class="yellow">틀린 코드</mark>

```jsx
function ProductPage({ product, addToCart }) {
  // 🔴 Effect 내부의 이벤트별 로직
  useEffect(() => {
    if (product.isInCart) {
      showNotification(`Added ${product.name} to the shopping cart!`);
    }
  }, [product]);

  function handleBuyClick() {
    addToCart(product);
  }

  function handleCheckoutClick() {
    addToCart(product);
    navigateTo("/checkout");
  }
  // ...
}
```

<br>

<mark class="yellow">올바른 코드</mark>

```jsx
function ProductPage({ product, addToCart }) {
  // ✅ 이벤트 핸들러에서 이벤트별 로직 호출
  function buyProduct() {
    addToCart(product);
    showNotification(`Added ${product.name} to the shopping cart!`);
  }

  function handleBuyClick() {
    buyProduct();
  }

  function handleCheckoutClick() {
    buyProduct();
    navigateTo("/checkout");
  }
  // ...
}
```

<br>
<br>

**3\. 무한 루프**

```jsx
useEffect(() => {
  setCount(count + 1); // 무한 루프 발생
}, [count]);
```

<br>
<br>

## <mark class="pink">6. useLayoutEffect</mark>

- 대부분의 Side Effect는 useEffect를 통한 비동기 처리가 권장된다.
- 하지만 DOM의 업데이트로 인해 발생하는 화면 깜빡임은 사용자 경험에 좋지 않다.
- 이러한 상황을 만났을 때 useLayoutEffect가 좋은 해결책이 될 수 있다.
- 로직이 복잡할 경우 사용자가 레이아웃을 보는데까지 시간이 오래걸린다는 단점이 있어, 기본적으로는 항상 useEffect 만을 사용하는 것을 권장한다.

**1\. useEffect**

- useEffect는 컴포넌트가 render와 paint까지 된 후에 비동기적으로 실행된다.
- 그래서 useEffect 내부에 DOM에 영향을 주는 코드가 있다면 화면 깜빡임이 생길 수 있다.

<br>

**2\. useLayoutEffect**

- useLayoutEffect는 컴포넌트가 render된 후에 동기적으로 실행된다.
- 그 후 마지막으로 paint가 실행된다.
- 그래서 useLayoutEffect 내부에 DOM에 영향을 주는 코드가 있어도 화면 깜빡임이 생기지 않는다.

![image](https://github.com/chlwlstlf/data/assets/63334368/fa359fc3-2169-47f3-9655-2c5555484b7a)
