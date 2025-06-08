---
layout: single
title: "[우테코] Level2 페이먼츠 2단계"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션1 💳페이먼츠 2단계

## <mark class="pink">🔥2단계 구현 사항</mark>

[2단계 - 페이먼츠](https://github.com/woowacourse/react-payments/pull/393){: .btn .btn--primary}

[페이먼츠 배포 사이트](https://chlwlstlf.github.io/react-payments/){: .btn .btn--primary}

[스토리북 배포 사이트](https://chlwlstlf.github.io/react-payments/storybook/){: .btn .btn--primary}

<br>
<br>

### <mark class="yellow">1. 커스텀 훅</mark>

![image](https://github.com/woowacourse/react-payments/assets/63334368/82cfeb6c-6ea7-4a06-8b60-1aab6e579889)

각 Input 필드를 커스텀 훅으로 다 분리하였다.  
각 Input 필드가 다 다른 기능을 하고 있어서 재사용을 고려하기가 어려웠다.  
재사용이 되지 않더라도 같은 관심사를 가진 기능끼리 UI로부터 분리하면 된다는 강의를 듣고 커스텀 훅 작성하였다.

<br>
<br>

### <mark class="yellow">2. 조건부 렌더링</mark>

각 input에 관한 state는 isNextVisible와 isValid 속성을 둘 다 가지고 있다.  
isNextVisible는 한번 true가 되면 false가 되지 않지만 isValid는 이후에 유효성 검사를 통과하지 못 하면 다시 false가 될 수 있다.  
isNextVisible는 다음 섹션은 렌더링할 때, isValid는 확인 버튼을 렌더링할 때 사용한다.

```jsx
const isNextVisible = userNameState.isNextVisible || isValid;
```

<div class="blue-box">
  <p>
    <div>1. isNextVisible와 isValid의 초깃값은 둘다 false</div>
    <div>2. isValid가 true가 되면 isNextVisible는 true가 되면서 다음 섹션이 열린다.</div>
    <div>3. 그 후로는 isValid가 false가 되어도 isNextVisible이 true인 상태이기 때문에 두 개를 or 연산하면 계속 true이다.</div>
    <div>4. 따라서 isValid 값이 바뀌어도 다음 섹션은 화면에 계속 보여지게 된다.</div>
  </p>
</div>

<br>
<br>

### <mark class="yellow">3. 자동 포커싱</mark>

새로운 섹션이 렌더링될 때 바로 포커싱될 수 있게 `autoFocus`를 사용하였다.  
카드 번호와 유효 기간은 연속으로 다음 Input에 포커싱이 될 수 있게 유효성 검사를 통과하면 `e.target.nextSibling`에 focus가 되게 하였다.

**autoFocus**

```jsx
<Input value={value} autoFocus></Input>
```

**e.target.nextSibling**

```jsx
const nextInput = e.target.nextSibling;
if (nextInput && nextInput instanceof HTMLInputElement) {
  nextInput.focus();
}
```

<br>
<br>

### <mark class="yellow">4. 숫자만, 영어만 입력할 수 있게</mark>

1단계 때는 숫자를 입력해야 하는 Input에서도 모든 입력을 받았다.  
2단계 때 숫자를 입력하는 곳에서는 숫자만, 영어만 입력하는 곳에서는 영어만 입력할 수 있게 하였다.

```jsx
const isPasswordValid = /^[0-9]*$/.test(value);

if (!isPasswordValid) return;

setPasswordState((prevState) => ({
  ...prevState,
  value: value,
  errorMessage,
  isNextVisible,
  isValid,
}));
```

<br>
<br>

### <mark class="yellow">5. 사용자 이름은 엔터치면 완료</mark>

사용자 이름을 한 글자만 쳐도 바로 cvc가 떠서 UX가 좋지 않았다.  
사용자 이름은 정해진 길이가 없기 때문에 엔터를 쳤을 때 유효성 검사를 통과하면 cvc 섹션이 뜨게 하였다.

```jsx
<Input
  value={userNameState.value}
  onChange={(e) => handleUserNameChange(e.target.value)}
  onKeyDown={handleKeyDown}
></Input>
```

```jsx
const handleKeyDown = (e) => {
  if (e.key === "Enter") {
    e.preventDefault();
    const isValid = !!userNameState.value && !userNameState.errorMessage[0];
    const isNextVisible = userNameState.isNextVisible || isValid;

    setUserNameState((prevState) => ({
      ...prevState,
      isValid,
      isNextVisible,
    }));
  }
};
```

<br>
<br>

### <mark class="yellow">6. 어떻게 children으로 넘겨줘야할까?</mark>

children을 inputComponents로 변경한 후 아래와 같이 props로 children에 들어가는 코드를 넘겨주었다.

```jsx
<NewCardInputSection
  mainText={FORM_FIELDS.PASSWORD.MAIN_TEXT}
  subText={FORM_FIELDS.PASSWORD.SUB_TEXT}
  label={FORM_FIELDS.PASSWORD.LABEL}
  errorMessage={PasswordState.errorMessage}
  inputComponents={
    <Input
      type="password"
      value={PasswordState.value}
      maxLength={FORM_FIELDS.PASSWORD.MAX_LENGTH}
      placeholder={FORM_FIELDS.PASSWORD.PLACEHOLDER}
      isError={!!PasswordState.errorMessage[0]}
      onChange={(e) => handlePasswordChange(e.target.value)}
      autoFocus
    ></Input>
  }
></NewCardInputSection>
```

<br>
<br>

### <mark class="yellow">7. 카드 프리뷰 3D로 띄우기</mark>

**1\. three.js 설치**

```bash
npm install three @react-three/fiber @react-three/drei @react-spring/three
```

`three`: 3D 그래픽을 다루는 라이브러리  
`@react-three/fiber`: Three.js의 React 래퍼  
`@react-three/drei`: Texture 로드, 카메라 제어 등 유틸 기능 제공  
`@react-spring/three`: 애니메이션을 위한 Spring 기반 라이브러리

<br>

**2\. 전체 코드**

```tsx
import { useRef } from "react";
import { Group } from "three";
import { Canvas } from "@react-three/fiber";
import { useSpring, a } from "@react-spring/three";
import { Html } from "@react-three/drei";

import * as Styled from "./CardPreview.styled";
import CardNumbers from "../cardNumbers/CardNumbers";
import CardExpiration from "../cardExpiration/CardExpiration";
import { CardCompany } from "../../../types/type";
import { getCardBrandImage } from "../../../utils/getCardBrand";

export interface CardPreviewProps {
  isCardFlipped: boolean;
  cvc: string;
  userName: string;
  cardExpiration: string[];
  cardCompany: CardCompany;
  cardNumbers: string[];
}

const CardPreview = ({
  isCardFlipped,
  cvc,
  userName,
  cardExpiration,
  cardCompany,
  cardNumbers,
}: CardPreviewProps) => {
  const cardBrandImage = getCardBrandImage(cardNumbers);
  const cardRef = useRef<Group | null>(null);

  const { rotationY } = useSpring({
    rotationY: isCardFlipped ? Math.PI : 0,
    config: { mass: 1, tension: 30, friction: 10 },
  });

  return (
    <Canvas
      style={{ width: "375px", height: "300px" }}
      camera={{ position: [0, 0, 5] }}
    >
      {/* 조명 설정 */}
      <ambientLight intensity={0.6} />
      <directionalLight intensity={0.5} position={[0, 7, 5]} />

      {/* 3D 카드 */}
      <a.group ref={cardRef} rotation-y={rotationY}>
        {/* 카드 앞면 */}
        <mesh position={[0, 0.5, 0.01]}>
          <planeGeometry args={[3.5, 2]} />
          <meshStandardMaterial />
          <Html transform>
            <Styled.FrontCard cardCompany={cardCompany}>
              <Styled.ChipSection>
                <Styled.ICChip />
                {cardBrandImage && (
                  <Styled.CardBrand>
                    <img src={cardBrandImage} alt="카드 브랜드 이미지" />
                  </Styled.CardBrand>
                )}
              </Styled.ChipSection>
              <Styled.CardInfoSection>
                <CardNumbers cardNumbers={cardNumbers} />
                <CardExpiration
                  month={cardExpiration[0]}
                  year={cardExpiration[1]}
                />
                <div>{userName && userName}</div>
              </Styled.CardInfoSection>
            </Styled.FrontCard>
          </Html>
        </mesh>

        {/* 카드 뒷면 */}
        <mesh position={[0, 0.5, -0.01]} rotation-y={Math.PI}>
          <planeGeometry args={[3.5, 2]} />
          <meshStandardMaterial />
          <Html transform>
            <Styled.BackCard>
              <Styled.CVC>{cvc}</Styled.CVC>
            </Styled.BackCard>
          </Html>
        </mesh>
      </a.group>
    </Canvas>
  );
};

export default CardPreview;
```

<br>

**3\. Canvas, Mesh 생성**

Three.js의 Canvas를 생성하고, 그 안에 3D Mesh(카드)를 배치한다.  
camera의 position은 [x, y, z]이며, z만 5로 지정하여 앞에서 카드를 정면으로 볼 수 있게 하였다.

{% raw %}

```tsx
<Canvas
  style={{ width: "375px", height: "300px" }}
  camera={{ position: [0, 0, 5] }}
>
  {/* 카드 */}
</Canvas>
```

{% endraw %}

<br>

**4\. 카드 회전 애니메이션**

`useSpring`은 React-Spring 라이브러리에서 제공하는 애니메이션을 위한 훅(Hook) 이다.  
React의 상태(state) 변경이 즉각적인 값 변경이라면, useSpring을 사용하면 부드럽게 변화하는 애니메이션 효과를 적용할 수 있다.

isCardFlipped 값에 따라 카드가 회전하는 애니메이션 적용했다.  
Math.PI 값으로 180도 회전한다.  
Math.PI: 파이 (180도)

```tsx
const { rotationY } = useSpring({
  rotationY: isCardFlipped ? Math.PI : 0,
  config: { mass: 1, tension: 30, friction: 10 },
});
```

<br>

**5\. 3D 카드에 HTML 요소 추가**

`<Html transform>`을 사용하면 3D 공간 안에서도 일반 HTML 요소를 렌더링할 수 있다.  
카드를 HTML + CSS로 구성하면서도, Three.js 환경에 배치할 수 있다.

<br>

**6\. 조명 추가**

```tsx
<ambientLight intensity={0.6} />
<directionalLight
  intensity={0.5}
  position={[0, 7, 5]}
/>
```

`ambientLight` → 전체적인 조명 (자연스러운 밝기 유지)
`directionalLight` → 특정 방향에서 오는 조명 (입체감 강조)

<video controls>
  <source src="https://github.com/user-attachments/assets/09e08dd7-0f3c-4efd-9df2-9bd6e32a6402" type="video/mp4">
</video>

<br>
<br>

## <mark class="pink">🔥2단계 피드백</mark>

### <mark class="yellow">1. 빈 문자열 처리</mark>

빈 문자열 처리 시 value만 쓰는 것은 옳지 않습니다. non-null인지를 보는 것과 빈 문자열 여부만 보는 것은, 데이터가 복잡해질수록 그 차이가 더 커지며 발생한 에러를 트래킹하는 것이 쉽지 않기 때문이다.

잘못된 코드

```ts
if (value) {
  return "문자가 입력되어있습니다.";
}
```

올바른 코드

```ts
if (value !== "") {
  return "문자가 입력되어있습니다.";
}
```
