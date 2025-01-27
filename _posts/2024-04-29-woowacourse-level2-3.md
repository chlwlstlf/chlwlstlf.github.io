---
layout: single
title: "[우테코] Level2 페이먼츠"
categories: woowacourse
toc: true
toc_sticky: true
---

# 💳 페이먼츠

## <mark class="pink">git action 배포 방법</mark>

1\. 깃허브 토큰 생성

2\. yml 만들기

.github > workflows > deploy.yml 코드

```ts
name: Deploy
on:
  push:
    branches: ["step1"] //배포하고 싶은 브랜치
  workflow_dispatch:
permissions:
  contents: read
  pages: write
  id-token: write
concurrency:
  group: "pages"
  cancel-in-progress: true
jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: 18
          cache: "npm"
      - name: Install dependencies
        run: npm install
      - name: Build
        run: npm run build
      - name: Build storybook //스토리북 배포
        run: |
          npm run build-storybook
          mv ./storybook-static ./dist/storybook
      - name: Deploy to gh-pages branch
        uses: peaceiris/actions-gh-pages@v3 //gh-gages에 배포
        with:
          github_token: ${{ secrets.TOKEN }} //깃허브 토큰
          publish_dir: ./dist //빌드했을 때 만들어진 폴더
```

3\. gh-pages

Settings > Pages에 들어가서 `Build and deployment`의 `Branch`를 gh-pages로 변경하면 몇 분 뒤에 사이트 주소가 만들어집니다.

## <mark class="pink">배포 사이트</mark>

[페이먼츠 배포 사이트](https://chlwlstlf.github.io/react-payments/)

[스토리북 배포 사이트](https://chlwlstlf.github.io/react-payments/storybook/)

## <mark class="pink">1단계 구조 설명</mark>

![KakaoTalk_20240418_175719919](https://github.com/woowacourse/react-payments/assets/63334368/f4524ea7-d960-4a01-a3ef-887e75c631ec)

## <mark class="pink">1단계 피드백</mark>

**[ 질문 사항 ]**

`State 관리`

1\. 하위 컴포넌트에 props로 setState를 넘겨 하위에서 state 변경이 일어나도록 하는 방법

```tsx
const ParentComponent = () => {
  const [open, setOpen] = setState(false);

  return <ChildComponent setOpen={setOpen} />;
};
```

2\. children과 onChange를 넘겨서 상위에서 state 변경이 일어나도록 하는 방법

```jsx
const ParentComponent = () => {
  const handleInputChange = () => {
    console.log("변경");
  };

  return <ChildComponent onChange={handleInputChange} />;
};
```

상태를 상위 컴포넌트에서 관리하는 방법을 선택했습니다.

왜냐하면 1번 방법은 하위 컴포넌트와 상위 컴포넌트 간의 결합도가 증가하고 2번 방법이 유지 보수하기에 더 용이하다고 판단했기 때문입니다.

<br>

**[ 피드백 ]**

컴포넌트 간 결합도가 높아지는 것이 반드시 나쁘다고는 할 수는 없습니다. 다만 어떠한 컴포넌트 내에 하위 컴포넌트에 대한 속성을 주입할 때 사용자로 하여금 얼마큼의 자유도를 부여할 것인지에 따라 설계가 달라질 수 있을 것 같습니다.

예를 들면,

1\. 컴포넌트 안에 하위 컴포넌트들을 선언하고 하위 컴포넌트에 대한 props를 외부에서 받을 때

```jsx
<FormField
  onChangeInput={}
  onClickLabel={}
  onError={}
  ...
/>
```

> 결합도가 높지만 사용자 입장에서는 props만 선언하면 되니 DX가 높음

<br>

2\. 컴포넌트 안에 하위 컴포넌트들이 들어갈 '위치'만 선언하고 외부에서 하위 컴포넌트를 받을 때

```jsx
<FormField
  renderInput={() => <Input />}
  renderLabel={() => <Label />}
  renderErrorText={() => <ErrorText />}
  ...
/>
```

> 컴포넌트의 동작, 레이아웃에 대한 관심사만 책임을 지며 View에 대해서는 사용처에서 커스텀

<br>

3\. Composition Pattern

```jsx
<FormField>
  <FormField.Input />
  <FormField.Label />
  <FormField.ErrorText />
</FormField>
```

> 컴포넌트의 동작에 대한 관심사만 가지며 사용처에서 레이아웃, View에 대한 높은 자유도를 얻음

때문에 단순히 결합도 측면에서 바라보기보다는,

구현하려는 컴포넌트에서 중요한 것이 '재사용성'인지, 혹은 '자유도'인지 폭넓게 고려하여 때에 따라 적절한 방식을 취해주면 좋을 것 같습니다.

<br>
<br>

## <mark class="pink">2단계 구현 설명</mark>

**1. 커스텀 훅**

![image](https://github.com/woowacourse/react-payments/assets/63334368/82cfeb6c-6ea7-4a06-8b60-1aab6e579889)

- 각 Input 필드를 커스텀 훅으로 다 분리하였습니다.
- 각 Input 필드가 다 다른 기능을 하고 있어서 재사용을 고려하기가 어려웠습니다.
- 재사용이 되지 않더라도 같은 관심사를 가진 기능끼리 UI로부터 분리하면 된다는 강의를 듣고 커스텀 훅 작성하였습니다.

<br>

**2. 조건부 렌더링**

- 각 input에 관한 state는 isNextVisible와 isValid 속성을 둘 다 가지고 있습니다.
- isNextVisible는 한번 true가 되면 false가 되지 않지만 isValid는 이후에 유효성 검사를 통과하지 못 하면 다시 false가 될 수 있습니다.
- isNextVisible는 다음 섹션은 렌더링할 때, isValid는 확인 버튼을 렌더링할 때 사용합니다.

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

## <mark class="pink">2단계 UI/UX 고려사항</mark>

**1. 자동 포커싱**

- 새로운 섹션이 렌더링될 때 바로 포커싱될 수 있게 `autoFocus`를 사용하였습니다.
- 카드 번호와 유효 기간은 연속으로 다음 Input에 포커싱이 될 수 있게 유효성 검사를 통과하면 `e.target.nextSibling`에 focus가 되게 하였습니다.

autoFocus

```jsx
<Input value={value} autoFocus></Input>
```

e.target.nextSibling

```jsx
const nextInput = e.target.nextSibling;
if (nextInput && nextInput instanceof HTMLInputElement) {
  nextInput.focus();
}
```

<br>

**2. 숫자만, 영어만 입력할 수 있게**

- 1단계 때는 숫자를 입력해야 하는 Input에서도 모든 입력을 받았습니다.
- 2단계 때 숫자를 입력하는 곳에서는 숫자만, 영어만 입력하는 곳에서는 영어만 입력할 수 있게 하였습니다.

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

**3. 사용자 이름은 엔터치면 완료**

- 사용자 이름을 한 글자만 쳐도 바로 cvc가 떠서 UX가 좋지 않았습니다.
- 사용자 이름은 정해진 길이가 없기 때문에 엔터를 쳤을 때 유효성 검사를 통과하면 cvc 섹션이 뜨게 하였습니다.

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

**4. 어떻게 children으로 넘겨줘야할까?**

- children을 inputComponents로 변경한 후 아래와 같이 props로 children에 들어가는 코드를 넘겨주었습니다.

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

## <mark class="pink">2단계 피드백</mark>

**빈 문자열 처리**

빈 문자열 처리 시 value만 쓰는 것은 옳지 않습니다. non-null인지를 보는 것과 빈 문자열 여부만 보는 것은, 데이터가 복잡해질수록 그 차이가 더 커지며 발생한 에러를 트래킹하는 것이 쉽지 않기 때문입니다.

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
