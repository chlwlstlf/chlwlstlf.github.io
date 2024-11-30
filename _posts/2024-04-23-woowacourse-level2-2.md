---
layout: single
title: "[우테코]Level2 Controlled & Uncontrolled Components, Hooks API"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션1 LMS 강의 자료2

## <mark class="pink">⛓ Controlled & Uncontrolled Components</mark>

**1\. Controlled Component (제어 컴포넌트)**

설명

- React에 값이 완전히 제어되는 Input Element
- State를 값으로 넘기고 그 State을 다룰 수 있는 핸들러를 콜백으로 넘긴다.
- 값을 \_\_\_ 으로 받는다

<br>

**2\. Uncontrolled Component (비제어 컴포넌트)**

설명

- 전통적인 HTML처럼 DOM에 제어되는 Input Element
- 오직 사용자만 값과 상호작용
- 값을 \_\_\_ 로 사용한다.

<br>
<br>

## <mark class="pink">⚓️ Hooks API</mark>

**React 과거의 문제**

- 재사용성에 대한 문제: class 기반에서는 로직을 재사용하는 방법이 복잡하게 느껴졌다.
- 중복과 관심사 분리에 대한 문제: 라이프사이클 메서드에 다양한 관심사의 로직이 조각조각 흩어지는 일이 자주 발생했다.
- 개념 자체로 인한 문제: this 바인딩을 이해하기 어렵다.

<br>

**React팀의 Solution, Hooks API**

Hook이란?

- Funtion Component에서도 React가 제공하는 모든 기능들을 가져다(hook into) 쓸 수 있게 해주는 React가 제공해주는 함수

**1\. useState - 상태**

```jsx
// useState hook 만들기
const [state, setState] = useState(initialState);

// 사용하기
setState(newState);
```

**2\. useEffect - 라이프사이클**

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

💡 Effect를 쓰지 않고 해결할 수 있다.

- useEffect는 컴포넌트를 '외부 시스템과 동기화'하기 위한 훅
- 이는 외부 시스템과 동기화하는 작업이 아니라면 useEffect 훅을 쓰지 않아도 된다는 말
