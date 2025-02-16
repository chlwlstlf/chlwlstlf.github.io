---
layout: single
title: "[우테코] Level2 Component, Storybook"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션1 LMS 강의 자료1

# 미션4 LMS 강의 자료1

## <mark class="pink">📖Component</mark>

### <mark class="yellow">1. 리액트에서의 컴포넌트</mark>

컴포넌트는 리액트의 가장 기본적인 단위이다.  
`더욱 큰 것의 일부`라는 표현으로 컴포넌트를 설명한다.

캡슐화 되어 있고, 재사용 및 재구성할 수 있다.  
상호 작용하는 여러 컴포넌트들을 '조합'해서 새로운 형태의 '합성' 컴포넌트를 만들 수 있다.

<br>
<br>

### <mark class="yellow">2. 스타일 가이드보다 중요한 것에 집중하자</mark>

Prettier 같은 포매터로 스타일 문제는 자동으로 해결하고, linter는 버그를 방지하는 데 초점을 맞춘다.  
만약 linter가 "불필요한 공백이 있다" 같은 스타일 문제만 계속 지적하고 있다면 그건 쓸데없는 노이즈일 뿐이다.

✅ 좋은 린트 설정을 하는 법  
"이 규칙이 실제로 버그를 방지하는 데 도움을 줬던 적이 있는가?" → 없다면 끈다.  
스타일 관련 규칙은 Prettier에게 맡기고, linter는 **"실제 문제를 잡는 도구"**로 활용한다.

<br>
<br>

### <mark class="yellow">3. 탄력적인 컴포넌트 디자인 원칙</mark>

React에서 진짜 중요한 건 스타일 가이드가 아니라, "컴포넌트가 유연하게 동작하는지"다.

<br>

1️⃣ 데이터 흐름을 멈추지 않는다.

React의 핵심 개념 중 하나는 **"props가 바뀌면, UI가 자동으로 업데이트된다"**는 것이다.

<br>

❌ 나쁜 예시: props를 state에 복사하는 패턴  
이 코드의 문제는 props가 변경돼도, state가 그대로라면 화면이 업데이트되지 않는다는 것이다.

```tsx
import React, { useState } from "react";

const Button = ({
  color,
  children,
}: {
  color: string;
  children: React.ReactNode;
}) => {
  const [buttonColor, setButtonColor] = useState(color); // ❌ props를 state에 복사

  return <button className={`Button-${buttonColor}`}>{children}</button>;
};
```

<br>

⭕ 올바른 코드: props를 직접 사용  
color가 변경되면 UI도 자동으로 반영된다.

```tsx
const Button = ({
  color,
  children,
}: {
  color: string;
  children: React.ReactNode;
}) => {
  return <button className={`Button-${color}`}>{children}</button>;
};
```

<br>

📌 그렇다면 "비싼 계산"이 필요할 때는?  
예를 들어, color 값이 변경될 때마다 색상을 계산하는 작업이 오래 걸린다면?
그럴 때는 useMemo를 사용해서 불필요한 계산을 방지할 수 있다.  
color가 바뀌지 않는 한 slowlyCalculateTextColor 함수는 다시 실행되지 않는다.

```tsx
import React, { useMemo } from "react";

const Button = ({
  color,
  children,
}: {
  color: string;
  children: React.ReactNode;
}) => {
  const textColor = useMemo(() => slowlyCalculateTextColor(color), [color]);

  return (
    <button className={`Button-${color} Button-text-${textColor}`}>
      {children}
    </button>
  );
};
```

<br>
<br>

2️⃣ 항상 렌더링할 준비가 되어 있어야 한다.

React의 핵심 철학은 **"언제든지 렌더링될 수 있다"**이다.

근데 어떤 사람들은 componentWillReceiveProps 같은 오래된 생명주기를 사용하면서 "특정한 타이밍에만 동작해야 해!" 라고 생각하는 경우가 있다. 이러면 부모 컴포넌트가 리렌더링될 때마다 의도치 않은 문제가 발생할 수도 있다.

<br>

❌ 나쁜 예시: 불필요한 state 동기화  
이런 패턴을 쓰면 부모가 리렌더링될 때마다 입력값이 초기화될 수 있다.

```tsx
import React, { useState, useEffect } from "react";

const TextInput = ({ value }: { value: string }) => {
  const [inputValue, setInputValue] = useState("");

  useEffect(() => {
    setInputValue(value); // ❌ 부모가 리렌더링될 때마다 값이 덮어씌워짐
  }, [value]);

  return (
    <input value={inputValue} onChange={(e) => setInputValue(e.target.value)} />
  );
};
```

<br>

⭕ 올바른 코드 1: 완전 제어된 컴포넌트  
값과 변경 로직을 부모에서 관리하는 방식을 사용하면 부모에서 value를 제어하면 렌더링이 많아져도 절대 깨지지 않는다.

```tsx
const TextInput = ({
  value,
  onChange,
}: {
  value: string;
  onChange: (e: React.ChangeEvent<HTMLInputElement>) => void;
}) => {
  return <input value={value} onChange={onChange} />;
};
```

<br>

⭕ 올바른 코드 2: 비제어 컴포넌트 + key 활용  
입력값을 내부에서 관리하고, 특정 조건에서 초기화할 필요가 있을 때만 key 변경.

```tsx
const TextInput = () => {
  const [value, setValue] = useState("");

  return <input value={value} onChange={(e) => setValue(e.target.value)} />;
};

// 특정 시점에서 상태를 초기화하려면 key를 변경
<TextInput key={formId} />;
```

<br>
<br>

3️⃣ 어떤 컴포넌트도 싱글톤이 아니다.

예를 들어, "내비게이션 바는 한 개만 있으니까 문제 없겠지?" 라고 생각할 수도 있다.

하지만 **"하나만 존재한다"**는 가정이 바뀌면?

레이아웃이 변경되면서 같은 내비게이션을 두 번 렌더링해야 할 수도 있다.  
페이지 전환 애니메이션을 만들 때, 이전 페이지와 새로운 페이지가 동시에 렌더링될 수도 있다.
항상 같은 컴포넌트가 여러 번 렌더링될 가능성을 염두에 두고 설계하자.

<br>
<br>

4️⃣ 로컬 상태를 격리하자.

모든 상태를 전역 상태로 관리할 필요는 없다.  
예를 들어 "내가 댓글을 펼쳤는지 여부" 같은 건 로컬 상태로 두는 게 맞다.

<br>

❌ 나쁜 예시: 모든 상태를 전역에서 관리  
모든 댓글의 확장 상태를 전역에서 관리하면 불필요한 상태 공유가 발생한다.

```tsx
const App = () => {
  const [expandedCommentId, setExpandedCommentId] = useState(null);

  return (
    <Comment
      expandedCommentId={expandedCommentId}
      setExpandedCommentId={setExpandedCommentId}
    />
  );
};

const Comment = ({ expandedCommentId, setExpandedCommentId }) => {
  const isOpen = expandedCommentId === 1;

  return (
    <div>
      <button onClick={() => setExpandedCommentId(isOpen ? null : 1)}>
        Toggle
      </button>
    </div>
  );
};
```

<br>

⭕ 올바른 코드: 로컬 상태를 사용하여 컴포넌트별로 독립적으로 관리  
이렇게 하면 각각의 Comment 인스턴스가 독립적으로 동작한다.

```tsx
const Comment = () => {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <div>
      <button onClick={() => setIsOpen(!isOpen)}>Toggle</button>
    </div>
  );
};
```

<br>
<br>

## <mark class="pink">📖Storybook</mark>

### <mark class="yellow">1. Storybook 기능</mark>

**정의**

UI 컴포넌트를 애플리케이션 외부의 독립된 환경에서 개발하기 쉽게 도움을 주는 테스트 도구

<br>

**역할**

- 개발자는 독립적인 환경에서 컴포넌트를 개발
- 개발환경에서 컴포넌트들과 인터랙티브하게 상호작용
- 다양한 부가 기능(addons)를 지원하여 커스터마이징하기 쉬운 유연한 API를 제공
- 정적 빌드하여 배포 가능
- 배포된 페이지를 통해 기획자, 디자이너와 같은 비 개발 직군과도 협업 가능

<br>
<br>

### <mark class="yellow">2. Storybook 시작하기</mark>

**1\. Storybook 설치**

```bash
npx sb init
```

<br>

**2\. Storybook 실행**

```bash
npm run storybook
```

<br>

**3\. 컴포넌트 구현**

1\. 숫자 표시 컴포넌트를 구현한다.  
2\. src 디렉토리에 components 폴더를 생성하고, 그 안에 `Display.tsx` 파일을 생성한다.  
3\. 아래 코드를 작성한다.

```tsx
export default function Display() {
  return <div>0</div>;
}
```

<br>

**4\. 스토리 파일 생성**

1\. src 디렉토리에 stories 폴더를 생성하고, `Display.stories.tsx` 파일을 생성한다.  
2\. 아래 코드를 작성한다.

```tsx
import type { Meta, StoryObj } from "@storybook/react";
import Display from "../components/Display";

const meta = {
  title: "Display",
  component: Display,
} satisfies Meta<typeof Display>;

export default meta;

type Story = StoryObj<typeof meta>;

export const Default: Story = {};
```

✅ 컴포넌트와 스토리 파일을 어떻게 분리할지는 팀에서 정하면 된다. Button 폴더에 컴포넌트와 스토리 파일을 둘다 넣어도 되고, components 폴더에는 컴포넌트만 stories 폴더에는 스토리만 넣어도 된다.

<br>
<br>

**참고**

[탄력적인 컴포넌트 작성하기](https://overreacted.io/writing-resilient-components/)
