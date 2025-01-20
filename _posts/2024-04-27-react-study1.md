---
layout: single
title: "[React]state 관리"
categories: react
toc: true
toc_sticky: true
---

# 리액트 스터디1 - state

[참고 자료-state 관리](https://ko.react.dev/learn/managing-state){: .btn .btn--primary}

## <mark class="pink">1. state로 입력에 반응하기</mark>

**1\. 선언형 UI 프로그래밍과 명령형 UI 프로그래밍의 차이점**

<mark class="yellow">리액트는?</mark>

- 리액트는 UI를 조작하는 선언적 방법을 제공하는 라이브러리다.
- React에서는 직접 UI를 조작하지 않는다.
- 대신 표시할 내용을 선언하면 React가 UI를 업데이트할 방법을 알아낸다.

<br>
<br>

<mark class="yellow">선언형 UI</mark>

설명

- 어떤 UI를 보여주고 싶은지 '선언'만 하면 알아서 그려준다.
- 컴포넌트나 데이터의 배치를 통해 무엇이 렌더링 될지에 대해서만 생각하면 된다.
- 원하는 UI 결과값만 리턴하고 어떻게 화면에 보여지게 할 지 로직은 추상화해둔다.
- 즉, UI를 다루는 부분은 리액트에게 위임하고 개발자는 결과에만 초점을 맞추자.

<br>

예시

- filter, map, reduce 등의 메서드 안에 동작이 서술되어 있기 때문에 개발자는 메서드가 어떻게 동작하는 지 알 필요 없이 그대로 선언에 사용하기만 하면 된다.

  ```jsx
  const numbers = [1, 2, 3, 4, 5, 6];
  let sum = numbers
    .filter((i) => i % 2)
    .map((i) => i * 2)
    .reduce((acc, cur) => acc + cur);

  console.log(sum); // 18
  ```

<br>
<br>

<mark class="yellow">명령형 UI</mark>

설명

- UI를 조작하기 위한 과정을 컴퓨터에게 알려주는 것이다.
- 어떤 로직으로 어떻게 코드를 짜야 페이지가 그려지는지 지시한다.
- 결과를 얻기 위해 데이터를 어떻게 조작해야 하는지 '과정'에 집중한다.
- 코드가 어떻게 동작해야 하는지를 작성한다.

<br>

예시

- 배열에 있는 숫자가 어떻게 반복되고 계산되는지에 대한 로직이 모두 드러남

  ```jsx
  const numbers = [1, 2, 3, 4, 5, 6];
  let sum = 0;

  for (let i = 0; i < numbers.length; i++) {
    if (i % 2 === 0) {
      sum += numbers[i] * 2;
    }
  }

  console.log(sum); // 18
  ```

<br>
<br>

**2\. state가 왜 필요한가?**

<mark class="yellow">state란?</mark>

- state는 간단하게 말해서 변수이다. 하지만 const, let 등으로 선언한 변수와 다르게 값이 변하면 관련 있는 컴포넌트들이 재렌더링되어 화면이 바뀐다.

- state는 컴포넌트의 내부에서 변경 가능한 데이터를 다루기 위해 사용하는 객체이다.

- 일반적으로 리액트에서는 유동적인 데이터는 변수에 담아서 사용하지 않고 useState()라는 리액트 함수를 사용하여 state라는 저장 공간에 담아 사용한다.

<br>
<br>

<mark class="yellow">state를 사용하는 이유</mark>

- 변수는 변경되어도 자동으로 화면이 바뀌지 않는다. 하지만 state는 변경되면 자동으로 화면이 바뀌기 때문에 state를 사용한다.

<br>
<br>

<mark class="yellow">setState</mark>

state도 결국 객체이기 때문에, 같은 키값을 가진 경우라면 가장 마지막 실행값으로 덮어씌워지는데 이는 객체를 합치는 함수인 Object.assign()에서 확인할 수 있다.

1\) 3씩 증가

```jsx
const plus = () => {
  setCount(count + 1);
  setCount(count + 2);
  setCount(count + 3);
};
```

콜백 함수를 사용하면 항상 최신의 값을 인자로 받아와서 처리하기 때문에 setCount(count => count + 1)를 쓰면 최신 값을 받아서 처리할 수 있다.

<br>

2\) 6씩 증가

```jsx
const plus = () => {
  setCount((count) => count + 1);
  setCount((count) => count + 2);
  setCount((count) => count + 3);
};
```

<br>
<br>

**3\. UI를 선언적인 방식으로 생각하기**

예시) 비필수적인 state 변수를 제거

<mark class="yellow">리팩토링 전</mark>

```jsx
const [isEmpty, setIsEmpty] = useState(true);
const [isTyping, setIsTyping] = useState(false);
const [isSubmitting, setIsSubmitting] = useState(false);
const [isSuccess, setIsSuccess] = useState(false);
const [isError, setIsError] = useState(false);
```

<br>
<br>

<mark class="yellow">리팩토링 과정</mark>

1\. state가 모순을 야기하나요?

- 예를 들어, isTyping 과 isSubmitting은 동시에 true일 수 없다.
- 이러한 모순은 일반적으로 state가 충분히 제약되지 않았음을 의미한다.
- 두 boolean의 조합은 네 가지가 가능하지만 유효한 state는 세 가지뿐이다.
- “불가능한” state를 제거하려면 세 가지 값을 하나의 status로 결합하면 된다: 'typing', 'submitting', 'success'.

<br>

2\. 다른 state 변수에 이미 같은 정보가 있나요?

- isEmpty와 isTyping은 동시에 true가 될 수 없다.
- 이를 각 state 변수로 분리하면 동기화되지 않아 버그가 발생할 위험이 있다.
- 다행히 isEmpty를 제거하고 대신 answer.length === 0으로 확인할 수 있다.

<br>

3\. 다른 state 변수를 뒤집으면 동일한 정보를 얻을 수 있나요?

- isError는 error !== null을 대신 확인할 수 있기 때문에 필요하지 않다.

<br>
<br>

<mark class="yellow">리팩토링 후</mark>

```jsx
const [answer, setAnswer] = useState("");
const [error, setError] = useState(null);
const [status, setStatus] = useState("typing"); // 'typing', 'submitting', 'success'
```

<br>
<br>

**4\. ⭐️ 참고**

즉, 모든 컴포넌트가 자신의 이벤트 핸들러에서 setState() 를 호출하기 전까지 기다린다. 이렇게 하면 불필요한 렌더링을 줄이면서, 성능을 향상시킬 수 있다

`setState` 의 호출은 비동기적으로 이루어진다. 따라서 setState 호출 직후 새로운 값이 state에 반영될 것이라고 생각하면 안된다.

React는 브라우저 이벤트가 끝날 시점에 state를 **일괄적으로 업데이트 한다.**

React에서는 16ms 단위로 batch update를 진행한다. 16ms 동안 변경된 상태 값들을 모아 리렌더링을 진행한다. ⇒ 모니터

<br>

[React에서의 setState](https://mingule.tistory.com/64)

1\. 첫번째 이유

state가 바뀔 때마다 렌더링 하면 효율성이 떨어지기 때문이다.

<br>

2\. 두번쨰 이유

내부적인 State, Props, Ref들에 대한 일관성을 보장할 수 있다. React는 재조정(reconciliation) 그리고 바뀌는 작업(flush) 이후에 State과 Props를 업데이트 하도록 만들어놨다. 이렇게 state가 바로 바뀌는 것이 아닌, 바뀌는 시간을 딜레이 함으로써

state, props 등의 일관성을 유지하여 애플리케이션의 안정성을 높인다.

state 를 생성할 때는 ‘반드시 필요한’ ‘핵심적인’ state 부터 선언하는 것으로 시작한다. 처음부터 효율적인 state를 설계하는 것이 힘들다면, 다 적어놓고 리팩토링한다.

<br>

3\. 세번째 이유

16 \* 60 = 960

1000ms = 1s

⇒ 모니터의 주사율 때문이다.

<br>
<br>

## <mark class="pink">2. State 구조 선택</mark>

> “state를 최대한 단순하게 만들되, 그보다 더 단순해서는 안 됩니다.”

**1\. 관련 state 그룹화하기**

<mark class="yellow">틀린 코드</mark>

```jsx
const [x, setX] = useState(0);
const [y, setY] = useState(0);
```

<br>

<mark class="yellow">올바른 코드</mark>

```jsx
const [position, setPosition] = useState({ x: 0, y: 0 });
```

<br>
<br>

**2\. 깊게 중첩된 state 피하기**

<mark class="yellow">틀린 코드</mark>

```jsx
export const initialTravelPlan = {
  id: 0,
  title: '(Root)',
  childPlaces: [
    {
      id: 1,
      title: "Earth",
      childPlaces: [
        {
          id: 2,
          title: "Africa",
          childPlaces: [
            {
              id: 3,
              title: "Botswana",
              childPlaces: [],
            },
          ],
        },
      ],
    },
  ];
};
```

<br>

<mark class="yellow">올바른 코드</mark>

```jsx
export const initialTravelPlan = {
  0: {
    id: 0,
    title: "(Root)",
    childIds: [1, 42, 46],
  },
  1: {
    id: 1,
    title: "Earth",
    childIds: [2, 10, 19, 26, 34],
  },
  2: {
    id: 2,
    title: "Africa",
    childIds: [3, 4, 5, 6, 7, 8, 9],
  },
  3: {
    id: 3,
    title: "Botswana",
    childIds: [],
  },
};
```

<br>
<br>

## <mark class="pink">3. 컴포넌트 간의 state 공유</mark>

**state 끌어올리기**

<mark class="yellow">설명</mark>

- 때로는 두 컴포넌트의 state가 항상 함께 변경되기를 원할 때가 있다.
- 그렇게 하려면 두 컴포넌트에서 state를 제거하고 가장 가까운 공통 부모로 이동한 다음 props를 통해 전달하면 된다.

<br>

<mark class="yellow">틀린 예시</mark>

![1](https://github.com/chlwlstlf/data/assets/63334368/b3249519-a35c-4800-80a2-4181a97e1135)

<br>

<mark class="yellow">올바른 예시</mark>

![2](https://github.com/chlwlstlf/data/assets/63334368/c1feb463-f362-437c-bfe6-b0e45f668cd2)

<br>
<br>

## <mark class="pink">4. state 보존 및 재설정</mark>

**1\. state는 트리의 한 위치에 묶인다**

<mark class="yellow">설명</mark>

- 컴포넌트에 state를 부여할 때, state가 컴포넌트 내부에 “존재”한다고 생각할 수 있다.
- 하지만 state는 실제로 React 내부에서 유지된다.
- React는 렌더링 트리에서 해당 컴포넌트의 위치에 따라 보유하고 있는 각 state를 올바른 컴포넌트와 연결한다.

<br>

<mark class="yellow">다른 위치</mark>

```jsx
export default function App() {
  return (
    <div>
      <Counter />
      <Counter />
    </div>
  );
}
```

```jsx
export default function App() {
  const [isFancy, setIsFancy] = useState(false);
  return (
    <div>
      {isFancy ? (
        <div>
          <Counter isFancy={true} />
        </div>
      ) : (
        <section>
          <Counter isFancy={false} />
        </section>
      )}
      <label>
        <input
          type="checkbox"
          checked={isFancy}
          onChange={(e) => {
            setIsFancy(e.target.checked);
          }}
        />
        Use fancy styling
      </label>
    </div>
  );
}
```

<br>

<mark class="yellow">같은 위치</mark>

> React에서 중요한 것은 JSX 마크업이 아니라 UI 트리에서의 위치라는 것을 기억하세요! 이 컴포넌트에는 if 내부와 외부에 서로 다른 <Counter /> JSX 태그가 있는 두 개의 return절이 있습니다

```jsx
export default function App() {
  const [isFancy, setIsFancy] = useState(false);
  return (
    <div>
      {isFancy ? <Counter isFancy={true} /> : <Counter isFancy={false} />}
    </div>
  );
}
```

<br>
<br>

**2\. 동일한 위치에서 state 재설정하기**

<mark class="yellow">잘못된 코드</mark>

```jsx
export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA ? <Counter person="Taylor" /> : <Counter person="Sarah" />}
      <button
        onClick={() => {
          setIsPlayerA(!isPlayerA);
        }}
      >
        Next player!
      </button>
    </div>
  );
}
```

<br>

<mark class="yellow">올바른 코드</mark>

1\) 컴포넌트를 다른 위치에 렌더링하기

```jsx
export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA && <Counter person="Taylor" />}
      {!isPlayerA && <Counter person="Sarah" />}
      <button
        onClick={() => {
          setIsPlayerA(!isPlayerA);
        }}
      >
        Next player!
      </button>
    </div>
  );
}
```

<br>

2\) 각 컴포넌트에 key로 명시적인 아이덴티티를 부여하기 (props는 상관x)

- key를 지정하면 React가 부모 내 순서가 아닌 key 자체를 위치의 일부로 사용하도록 지시한다.
- 그렇기 때문에 JSX에서 같은 위치에 렌더링하더라도 React의 관점에서 보면 두 카운터는 서로 다른 카운터이다.
- 결과적으로 state를 공유하지 않는다. 카운터가 화면에 나타날 때마다 그 state가 생성된다.
- 카운터가 제거될 때마다 그 state는 소멸된다. 두 카운터 사이를 토글하면 state가 계속 초기화된다.

```jsx
export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA ? (
        <Counter key="Taylor" person="Taylor" />
      ) : (
        <Counter key="Sarah" person="Sarah" />
      )}
      <button
        onClick={() => {
          setIsPlayerA(!isPlayerA);
        }}
      >
        Next player!
      </button>
    </div>
  );
}
```

<br>
<br>

## <mark class="pink">5. state 로직을 reducer로 작성하기</mark>

**1\. reducer 함수란?**

- 한 컴포넌트에서 state 업데이트가 여러 이벤트 핸들러로 분산되는 경우가 있다.
- 이 경우 컴포넌트를 관리하기 어려워진다.
- 따라서, 이 문제 해결을 위해 state를 업데이트하는 모든 로직을 reducer를 사용해 컴포넌트 외부로 단일 함수로 통합해 관리할 수 있다.

<br>
<br>

**2\. useState에서 useReducer로 리펙토링 하는 방법**

<mark class="yellow">1. state를 설정하는 것에서 action을 dispatch 함수로 전달하는 것으로 바꾸기</mark>

useState 코드

```jsx
function handleAddTask(text) {
  setTasks([
    ...tasks,
    {
      id: nextId++,
      text: text,
      done: false,
    },
  ]);
}

function handleChangeTask(task) {
  setTasks(
    tasks.map((t) => {
      if (t.id === task.id) {
        return task;
      } else {
        return t;
      }
    })
  );
}

function handleDeleteTask(taskId) {
  setTasks(tasks.filter((t) => t.id !== taskId));
}
```

<br>

useReducer로 코드

```jsx
function handleAddTask(text) {
  // "action" 객체:
  dispatch({
    type: "added",
    id: nextId++,
    text: text,
  });
}

function handleChangeTask(task) {
  dispatch({
    type: "changed",
    task: task,
  });
}

function handleDeleteTask(taskId) {
  dispatch({
    type: "deleted",
    id: taskId,
  });
}
```

<br>
<br>

<mark class="yellow">2. reducer 함수 작성하기</mark>

<div class="blue-box">
  <p>
    <div>1. 첫 번째 인자에 현재 state (tasks) 선언하기</div>
    <div>2. 두 번째 인자에 action 객체 선언하기</div>
    <div>3. reducer에서 다음 state 반환하기 (React가 state에 설정하게 될 값)</div>
  </p>
</div>

```jsx
function tasksReducer(tasks, action) {
  switch (action.type) {
    case "added": {
      return [
        ...tasks,
        {
          id: action.id,
          text: action.text,
          done: false,
        },
      ];
    }
    case "changed": {
      return tasks.map((t) => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case "deleted": {
      return tasks.filter((t) => t.id !== action.id);
    }
    default: {
      throw Error("Unknown action: " + action.type);
    }
  }
}
```

<br>
<br>

<mark class="yellow">3. 컴포넌트에서 reducer 사용하기</mark>

```jsx
const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);
```

<br>
<br>

## <mark class="pink">6. Context를 사용해 데이터를 깊게 전달하기</mark>

**1\. "Prop drilling" 이란?**

- Props 전달하기는 UI 트리를 통해 명시적으로 데이터를 사용하는 컴포넌트에 전달하는 훌륭한 방법이다.

- 하지만 이 방식은 어떤 prop을 트리를 통해 깊이 전해줘야 하거나, 많은 컴포넌트에서 같은 prop이 필요한 경우에 장황하고 불편할 수 있다.

- 데이터가 필요한 여러 컴포넌트의 가장 가까운 공통 조상은 트리 상 높이 위치할 수 있고 그렇게 높게까지 state를 끌어올리는 것은 “Prop drilling”이라는 상황을 초래할 수 있다.

<br>

**2\. "Context" 란?**

- 부모가 트리 내부 전체에, 심지어 멀리 떨어진 컴포넌트에조차 어떤 데이터를 제공할 수 있도록 한다.

<br>

**3\. Context 사용하기**

<mark class="yellow">1. Context 생성하기</mark>

```jsx
import { createContext } from "react";

export const LevelContext = createContext(1);
```

<br>

<mark class="yellow">2. Context 사용하기</mark>

```jsx
export default function Heading({ children }) {
  const level = useContext(LevelContext);
  // ...
}
```

<br>

<mark class="yellow">3. Context 제공하기</mark>

```jsx
import { useContext } from "react";
import { LevelContext } from "./LevelContext.js";

export default function Section({ children }) {
  const level = useContext(LevelContext);
  return (
    <section className="section">
      <LevelContext.Provider value={level + 1}>
        {children}
      </LevelContext.Provider>
    </section>
  );
}
```

<br>
<br>

**4\. Context 사용 예시**

- 테마 지정하기
- 현재 계정
- 라우팅
- 상태관리
