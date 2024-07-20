---
layout: single
title: "[우테코]storybook에 theme, 절대경로 적용하기"
categories: woowacourse
toc: true
toc_sticky: true
---

# storybook 사용하면서 겪었던 시행착오

## <mark style='background-color: #ffdce0'>📌storybook에 theme 적용하기</mark>

> storybook에서 theme이 적용이 안된다!!!!!

ThemeProvider를 사용하여 공통 theme을 관리하고 있었는데 이걸 storybook 인식을 하기 못 하여 오류가 발생했습니다.

<br>

**index.tsx**

```tsx
import React from "react";
import ReactDOM from "react-dom/client";
import { RouterProvider } from "react-router-dom";
import { ThemeProvider } from "styled-components";
import router from "@/router";
import GlobalStyles from "@/styles/globalStyles";
import { theme } from "@/styles/theme";

ReactDOM.createRoot(document.getElementById("root") as HTMLElement).render(
  <React.StrictMode>
    <ThemeProvider theme={theme}>
      <GlobalStyles />
      <RouterProvider router={router} />
    </ThemeProvider>
  </React.StrictMode>
);
```

<br>

**1\. 코드 수정**

**.storybook > preview.tsx**

- 원래는 preview.ts인데 decorator를 적용하기 위해 tsx로 변경합니다.
- 그 후 `Story` 를 `ThemeProvider` 로 감싸주면 됩니다.

```tsx
import GlobalStyles from "../src/styles/globalStyles";
import { theme } from "../src/styles/theme";
import type { Preview } from "@storybook/react";
import React from "react";
import { ThemeProvider } from "styled-components";

const preview: Preview = {
  parameters: {
    controls: {
      matchers: {
        color: /(background|color)$/i,
        date: /Date$/i,
      },
    },
  },
};

export const decorators = [
  (Story) => (
    <ThemeProvider theme={theme}>
      <GlobalStyles />
      <Story />
    </ThemeProvider>
  ),
];

export default preview;
```

<br>
<br>

## <mark style='background-color: #ffdce0'>📌storybook에 절대 경로 설정하기</mark>

**1\. 패키지 설치**

- TypeScript 프로젝트에서 절대 경로를 설정하고, 이를 Webpack에서 해석할 수 있도록 돕는 플러그인입니다.

```bash
npm install -D tsconfig-paths-webpack-plugin
```

<br>

**2\. 코드 수정**

**.storybook > main.ts**

- 아래 ‘이 부분!!’을 추가해주면 됩니다.
- `config.resolve`와 `config.resolve.plugins`가 undefined될 수 있다는 오류가 뜨기 때문에 조건문을 추가해주었습니다.

```tsx
import type { StorybookConfig } from "@storybook/react-webpack5";

const TsconfigPathsPlugin = require("tsconfig-paths-webpack-plugin"); //이 부분!!

const config: StorybookConfig = {
  stories: ["../src/**/*.stories.@(js|jsx|mjs|ts|tsx)"],
  addons: [
    "@storybook/addon-webpack5-compiler-swc",
    "@storybook/addon-onboarding",
    "@storybook/addon-links",
    "@storybook/addon-essentials",
    "@chromatic-com/storybook",
    "@storybook/addon-interactions",
  ],
  framework: {
    name: "@storybook/react-webpack5",
    options: {},
  },
  docs: {
    autodocs: true,
  },

  // 이 부분!!
  webpackFinal: async (config) => {
    if (!config.resolve) {
      config.resolve = {};
    }
    if (!config.resolve.plugins) {
      config.resolve.plugins = [];
    }
    config.resolve.plugins.push(new TsconfigPathsPlugin({}));
    return config;
  },
  //
};
export default config;
```
