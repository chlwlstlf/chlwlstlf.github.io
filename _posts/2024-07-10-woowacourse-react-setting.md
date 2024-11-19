---
layout: single
title: "[우테코]Level3 React 초기 세팅"
categories: woowacourse
toc: true
toc_sticky: true
---

# CRA없이 React 세팅하기

## <mark class="pink">📌들어가기</mark>

- 보통 `npx create-react-app 프로젝트명`으로 리액트 초기 세팅을 쉽게 한 후 프로젝트를 시작합니다.
- 위와 같은 방법을 CRA라고 하는데 우테코에서는 CRA를 사용하지 않고 프로젝트를 시작하는 것이 미션이었습니다.
- React, React DOM, Webpack, Typescript, ESLint, Prettier, Jest를 설치 & 설정하였습니다.

<br>
<br>

## <mark class="pink">📌1. React 설정</mark>

**1\. 프로젝트 디렉토리 설정**

- 프로젝트 디렉토리를 만들고 해당 디렉토리로 이동합니다.

```bash
mkdir [프로젝트명]
cd [프로젝트명]
```

<br>

**2\. NPM 초기화**

- 새 npm 프로젝트를 초기화합니다.
- `-y`는 모든 질문에 yes를 한다는 명령어로 궁금하다면 `-y`를 없앤 후 질문을 하나하나 읽어보셔도 좋습니다.

```bash
npm init -y
```

<br>

**3\. React 및 React DOM 설치**

- 초기화가 끝났다면 React의 기본 패키지인 React와 React DOM 패키지를 설치합니다.

```bash
npm install react react-dom
```

<br>
<br>

## <mark class="pink">📌2. Typescript 설정</mark>

**1\. typescript 설치**

- `typescript`: 타입스크립트 설치
- `ts-loader`: Webpack을 사용하여 TypeScript 파일을 번들링합니다.
- `@types/react`: React의 TypeScript 타입 정의
- `@types/react-dom`: React DOM의 TypeScript 타입 정의

```bash
npm i -D typescript ts-loader @types/react @types/react-dom
```

<br>

**2\. tsconfig.json 생성**

```json
{
  "compilerOptions": {
    "lib": [
      "DOM",
      "DOM.Iterable",
      "ESNext"
    ] /* Document, window 등의 라이브러리 타입 추가 */,
    "target": "ESNext" /* Typescript 가 변환할 JavaScript의 ECMAScript 버전 */,
    "jsx": "react-jsx" /* react 의 jsx 사용하도록 설정 */,
    "module": "ESNext" /* TypeScript 컴파일러가 생성하는 JavaScript 모듈 코드의 형식 */,
    "moduleResolution": "Node" /* 패키지를 node_modules 에서 찾도록 설정 */,
    "baseUrl": "./" /* 기준이 되는 디렉토리 */,
    "resolveJsonModule": true /* json 파일을 모듈로 가져올 수 있다 */,
    "allowJs": false /* 자바스크립트를 허용할지 여부 */,
    "esModuleInterop": true /* ESM, CJS 모듈 호환 여부 */,
    "forceConsistentCasingInFileNames": true /* import 시 파일 이름 대소문자 구분 여부 */,

    "strict": true /* Enable all strict type-checking options. */,
    "noFallthroughCasesInSwitch": true /* switch case 문에서 case 별 break 넣지 않으면 에러 발생 여부  */,
    "skipLibCheck": true /* d.ts 파일을 무시할지 여부 */,
    "paths": {
      "@/*": ["src/*"]
    }
  },
  "include": ["src/**/*"]
}
```

<br>
<br>

## <mark class="pink">📌3. Webpack 설정</mark>

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>Vite 등의 다른 도구를 도입하는 경우가 늘고는 있으나, 아직까지는 프로덕션에서 Webpack 기반으로 이미 구성된 프로젝트가 많은 것으로 판단하였습니다.</p>
  <p>이에 따라 Vite 대신 Webpack을 기반으로 프로젝트를 빌드하게 되었습니다.</p>
</div>

<br>

**1\. Webpack 관련 패키지 설치**

- `-D`는 Dev server에서만 사용한다는 뜻입니다.
- `webpack`: 모듈 번들러입니다.
- `webpack-cli`: Webpack을 실행하기 위한 명령줄 도구를 제공합니다.
- `webpack-dev-server`: 소스 코드의 변경 사항은 수동으로 새로 고칠 필요 없이 브라우저에 즉시 반영됩니다. 개발 중에는 파일을 제공하고 실시간 다시 로드 기능을 제공하기 위해 로컬 서버가 필요한 경우가 많습니다. 페이지를 새로 고치지 않고도 실시간으로 변경 사항을 확인할 수 있으므로 개발 프로세스가 훨씬 더 효율적이 됩니다.
- `clean-webpack-plugin`: 이전 빌드를 삭제하여 깨끗한 빌드를 보장하는 플러그인
- `html-webpack-plugin`: HTML 파일을 생성하거나 수정하여 빌드된 자바스크립트 파일을 자동으로 포함시켜주는 플러그인

```bash
npm i -D webpack webpack-cli webpack-dev-server clean-webpack-plugin html-webpack-plugin
```

<br>

**2\. package.json 코드 수정**

dev

- `webpack-dev-server`: Webpack Dev Server를 실행합니다.
- `--mode=development`: 개발 모드로 실행합니다. 개발 모드에서는 소스맵과 같이 디버깅에 유용한 기능들이 활성화됩니다.
- `--open`: 서버가 시작될 때 기본 웹 브라우저를 자동으로 엽니다.
- `--hot`: 핫 모듈 교체(HMR)를 활성화합니다. HMR은 코드 변경 시 전체 페이지를 새로고침하지 않고도 변경된 모듈만 즉시 교체해 줍니다.
- `--progress`: 빌드 진행 상황을 콘솔에 표시합니다.

---

build

- `webpack`: 이걸 적어줘야 webpack으로 빌드합니다.
- `--mode=production`: Webpack을 프로덕션 모드로 실행합니다. 프로덕션 모드에서는 코드 최적화, 압축, 난독화 등의 작업이 자동으로 이루어집니다.

```json
"scripts": {
  "dev": "webpack-dev-server --mode=development --open --hot --progress",
  "build": "webpack --mode=production --progress",
  //나머지 코드
}
```

<br>

**3\. webpack.config.js 파일 생성**

```js
import { CleanWebpackPlugin } from "clean-webpack-plugin";
import HtmlWebpackPlugin from "html-webpack-plugin";
import path from "path";
import webpack from "webpack";

module.exports = (env, argv) => {
  const prod = argv.mode === "production";

  return {
    mode: prod ? "production" : "development",
    devtool: prod ? "hidden-source-map" : "eval",
    entry: "./src/index.tsx",
    output: {
      path: path.join(__dirname, "/dist"),
      filename: "index.js",
    },
    devServer: {
      port: 3000,
      hot: true, //HMR 활성화
    },
    resolve: {
      extensions: [".js", ".jsx", ".ts", ".tsx"],
      alias: {
        "@": path.resolve(__dirname, "./src/"),
      },
    },
    devtool: prod ? false : "source-map",
    module: {
      rules: [
        {
          test: /\.tsx?$/,
          use: ["ts-loader"],
        },
      ],
    },
    plugins: [
      new webpack.ProvidePlugin({
        React: "react",
      }),
      new HtmlWebpackPlugin({
        template: "./public/index.html",
        minify:
          process.env.NODE_ENV === "production"
            ? {
                collapseWhitespace: true, // 빈칸 제거
                removeComments: true, // 주석 제거
              }
            : false,
      }),
      new CleanWebpackPlugin(),
    ],
  };
};
```

<br>

**4\. 개발 서버 실행**

```bash
npm run dev
```

<br>

**5\. webpack으로 빌드**

```bash
npm run build
```

<br>
<br>

## <mark class="pink">📌4. ESlint 설정</mark>

**1\. eslint 관련 패키지 설치**

- `eslint`: TypeScript 코드의 오류를 찾고 일관된 코드 스타일을 유지하기 위해 사용되는 린터(linter)입니다.
- `eslint-plugin-react`: JSX 문법 및 React-specific 규칙을 적용하기 위한 플러그인
- `eslint-plugin-prettier`: Prettier와 ESLint를 통합하여 Prettier의 코드 포매팅 규칙을 ESLint 규칙으로 적용합니다.
- `@typescript-eslint/eslint-plugin`: TypeScript 코드의 린팅을 지원하는 ESLint 플러그인입니다.
- `@typescript-eslint/parser`: TypeScript 코드를 ESLint가 이해할 수 있도록 파싱해주는 파서(parser)입니다.
- `eslint-plugin-react-hooks`: React Hooks의 규칙을 린팅하기 위한 ESLint 플러그인입니다. (use-)
- `eslint-plugin-storybook`: Storybook 파일의 린팅을 지원하는 ESLint 플러그인입니다.

```bash
npm install -D eslint eslint-plugin-react eslint-plugin-prettier @typescript-eslint/eslint-plugin @typescript-eslint/parser eslint-plugin-react-hooks eslint-plugin-storybook
```

<br>

**2\. .eslintrc.json 생성**

- `ignorePatterns`: 린팅을 무시할 파일이나 디렉토리를 지정합니다.
- `"eqeqeq": "error"`: 엄격한 동등 연산자(=== 및 !==) 사용을 강제합니다.
- `"@typescript-eslint/no-unused-vars": "off"`: 사용하지 않는 변수에 대한 경고를 끕니다.
- `"no-nested-ternary": "error"`: 중첩 삼항 연산자를 금지합니다.
- `"@typescript-eslint/no-non-null-assertion": "error"`: 단언 연산자(!) 사용을 금지합니다.
- `"no-console": "warn"`: console 사용 시 경고를 표시합니다.
- `"prettier/prettier": "error"`: Prettier 규칙을 위반할 경우 오류로 처리합니다.

```json
{
  "parser": "@typescript-eslint/parser",
  "extends": [
    "eslint:recommended",
    "plugin:react/recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:react-hooks/recommended",
    "plugin:prettier/recommended",
    "plugin:storybook/recommended"
  ],
  "plugins": ["prettier"],
  "ignorePatterns": ["dist", ".eslintrc.json"],
  "rules": {
    "react/react-in-jsx-scope": "off",
    "react/jsx-filename-extension": [
      1,
      {
        "extensions": [".ts", ".tsx"]
      }
    ],
    "eqeqeq": "error",
    "@typescript-eslint/no-unused-vars": "off",
    "no-nested-ternary": "error",
    "@typescript-eslint/no-non-null-assertion": "error",
    "no-console": "warn",
    "prettier/prettier": "error"
  }
}
```

<br>
<br>

## <mark class="pink">📌5. Prettier 설정</mark>

**1\. prettier 설치**

- `prettier`: 코드 스타일 가이드라인을 자동으로 적용하여 팀 전체의 코드 스타일을 일관성 있게 유지합니다.
- `@trivago/prettier-plugin-sort-imports`: import 구문을 자동으로 정렬해주는 기능을 추가합니다.

```bash
npm install -D prettier @trivago/prettier-plugin-sort-imports
```

<br>

**2\. .prettierrc 생성**

- `printWidth`: 한 줄에 출력할 최대 문자 수를 설정합니다.
- `tabWidth`: 탭을 사용할 때 공백의 개수를 설정합니다.
- `trailingComma`: 배열이나 객체에 후행 쉼표를 추가합니다.
- `semi`: 문장의 끝에 세미콜론을 사용합니다.
- `singleQuote`: 문자열에 큰 따옴표를 사용합니다. 여기서 eslint와 충돌이 나기 때문에 `eslint-plugin-prettier`를 설치했습니다.
- `bracketSpacing`: `{ foo: bar }` 형태로 공백을 추가합니다.
- `endOfLine`: 파일의 끝에 사용하는 줄바꿈 문자를 설정합니다.
- `plugins`: 아까 설치했던 패키지를 추가하여 import 구문을 정렬할 수 있게 합니다.
- `importOrder`: import 구문의 정렬 순서를 설정합니다. 이 순서는 팀원들과 상의하여 정하면 됩니다.
- `importOrderSortSpecifiers`: import 문 내에서 각 모듈을 알파벳 순서로 정렬합니다.
- `importOrderSeparation`: import 구문 사이에 빈 줄을 추가할지 여부를 설정합니다.

```json
{
  "printWidth": 100,
  "tabWidth": 2,
  "trailingComma": "all",
  "semi": true,
  "singleQuote": false,
  "bracketSpacing": true,
  "endOfLine": "auto",
  "plugins": ["@trivago/prettier-plugin-sort-imports"],
  "importOrder": ["^@/hooks/", "^@/components/", "^@/pages/", "^@/"],
  "importOrderSortSpecifiers": true,
  "importOrderSeparation": false
}
```

<br>
<br>

## <mark class="pink">📌6. Jest 설정</mark>

**1\. jest 설치**

- `jest`: RTL 중 단위 테스트를 하기 위해 설치했습니다.
- `@types/jest`: Jest를 TypeScript로 작성된 프로젝트에서 사용할 때 타입 지원을 제공하여 코드 작성 시 타입 오류를 방지하고, 자동 완성을 돕습니다.
- `ts-jest`: TypeScript로 작성된 파일을 테스트할 수 있도록 Jest와 TypeScript를 통합해줍니다. 이를 통해 TypeScript 코드의 테스트를 쉽게 수행할 수 있습니다.
- `@testing-library/react`: React 컴포넌트를 테스트하기 위한 유틸리티 라이브러리입니다.
- `@testing-library/jest-dom`: Jest 환경에서 DOM 노드를 위한 추가적인 매처(matchers)를 제공합니다. `toBeInTheDocument()`와 같은 추가 매처를 제공합니다.
- `jest-environment-jsdom`: JSDOM은 브라우저 없이도 Node.js 환경에서 DOM을 시뮬레이션하여 DOM API를 사용할 수 있게 해줍니다. React 컴포넌트와 같은 DOM 의존성이 있는 코드를 테스트할 때 유용합니다.

```bash
npm install -D jest @types/jest ts-jest @testing-library/react @testing-library/jest-dom jest-environment-jsdom
```

<br>

**2\. jest.config.mjs 생성**

- `testEnvironment`: 테스트 환경을 설정합니다. 여기서는 jsdom을 사용하여 브라우저 없이 DOM API를 시뮬레이션합니다.
- `transform`: `ts-jest`를 사용하여 TypeScript 파일을 JavaScript로 변환하고, Jest가 이를 테스트할 수 있게 합니다.
- `moduleNameMapper`: 모듈 경로 및 파일 형식을 매핑하여 Jest가 올바르게 모듈을 해석할 수 있게 합니다.

```js
export default {
  testEnvironment: "jsdom",
  transform: {
    "^.+\\.ts?$": "ts-jest",
  },
  moduleNameMapper: {
    "^.+\\.svg$": "jest-svg-transformer",
    "\\.(css|less|sass|scss)$": "identity-obj-proxy",
    "^@/(.*)": "<rootDir>/src/$1",
  },
};
```
