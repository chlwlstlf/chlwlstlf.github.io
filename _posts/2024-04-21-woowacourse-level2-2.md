---
layout: single
title: "[우테코] Level2 페이먼츠 1단계"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션1 💳페이먼츠 1단계

## <mark class="pink">🔥1단계 구현 사항</mark>

[1단계 - 페이먼츠](https://github.com/woowacourse/react-payments/pull/346){: .btn .btn--primary}

[페이먼츠 배포 사이트](https://chlwlstlf.github.io/react-payments/)

[스토리북 배포 사이트](https://chlwlstlf.github.io/react-payments/storybook/)

<br>
<br>

### <mark class="yellow">1. git action 배포 방법</mark>

**1\. 깃허브 토큰 생성**

**2\. yml 만들기**

.github > workflows > deploy.yml

```yml
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

**3\. gh-pages**

Settings > Pages에 들어가서 `Build and deployment`의 `Branch`를 gh-pages로 변경하면 몇 분 뒤에 사이트 주소가 만들어집니다.

<br>
<br>

### <mark class="yellow">1. 1단계 구조 설명</mark>

![KakaoTalk_20240418_175719919](https://github.com/woowacourse/react-payments/assets/63334368/f4524ea7-d960-4a01-a3ef-887e75c631ec)

<br>
<br>

## <mark class="pink">🔥1단계 피드백</mark>

**[질문]**

**State 관리**

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

상태를 상위 컴포넌트에서 관리하는 방법을 선택했다.

왜냐하면 1번 방법은 하위 컴포넌트와 상위 컴포넌트 간의 결합도가 증가하고 2번 방법이 유지 보수하기에 더 용이하다고 판단했기 때문이다.

<br>

**[피드백]**

컴포넌트 간 결합도가 높아지는 것이 반드시 나쁘다고는 할 수는 없다. 다만 어떠한 컴포넌트 내에 하위 컴포넌트에 대한 속성을 주입할 때 사용자로 하여금 얼마큼의 자유도를 부여할 것인지에 따라 설계가 달라질 수 있을 것 같다.

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
구현하려는 컴포넌트에서 중요한 것이 '재사용성'인지, 혹은 '자유도'인지 폭넓게 고려하여 때에 따라 적절한 방식을 취해주면 좋을 것 같다.
