---
layout: single
title: "[React]Webpack"
categories: react
toc: true
toc_sticky: true
---

# 리액트 스터디4 - webpack

## <mark class="pink">📌모듈이란?</mark>

- 개발하면서 작성한 모든 파일
- 자바스크립트에서의 모듈: 특정 기능을 가진 코드들을 하나의 파일로 관리하는 것
- 웹팩에서의 모듈: HTML, CSS, JS, Images, Font 등 웹 애플리케이션을 구성하는 모든 자원들

<br>
<br>

## <mark class="pink">📌자바스크립트 모듈 관리의 필요성</mark>

**과거**

- 자바스크립트의 변수 유효 범위(스코프)는 기본적으로 전역 범위를 기본으로 가진다.
- 모듈화 하지 않으면 어디서든 변수에 접근할 수 있고 실수로 변경할 수 있는 문제가 발생한다.

**현재**

- 이러한 문제를 해결하기 위해 모듈로 관리하게 되었다.
- CommonJS, AMD, ESModule 등의 모듈 시스템이 만들어졌다.

<br>
<br>

## <mark class="pink">📌로딩 시간 줄이는 법?</mark>

브라우저 요청하는 http 개수를 줄이는 것

→ 여러 모듈 js를 bundle.js로 묶어서 배포: **번들링**

<br>
<br>

## <mark class="pink">📌Webpack의 등장 배경</mark>

1\. 파일 단위의 자바스크립트 모듈 관리의 필요성
2\. 웹 개발 작업 자동화 도구
3\. 웹 애플리케이션의 빠른 로딩 속도와 높은 성능

<br>
<br>

## <mark class="pink">📌Webpack: 모듈 번들러</mark>

- 웹 애플리케이션에서 사용하는 수많은 리소스를 하나의 파일로 병합 및 압축해주는 “모듈 번들러”
- 번들링과 컴파일을 결합하는 “정적 모듈 번들러”

※ 빌드 = 번들링 = 변환

![Untitled (15)](https://github.com/chlwlstlf/data/assets/63334368/fcdce6dc-78ba-41d6-b1e1-565416abedf9)

개발자는 NPM을 사용하여 프로젝트에 필요한 Webpack 및 기타 JavaScript 라이브러리와 도구를 설치한다. 이는 일반적으로 npm install webpack 또는 npm install React와 같은 명령을 실행하여 수행된다.

![Untitled (16)](https://github.com/chlwlstlf/data/assets/63334368/fc2dc732-95f0-4bd7-8610-3b80a66b8f65)

<br>
<br>

## <mark class="pink">📌Webpack 이용시 장단점</mark>

**단점**

- 비교적 복잡한 configuration
- 개발 모드 속도(Webpack 5에서 추가된 caching 옵션으로 해결 완)
- Webpack의 번들 크기

**장점**

- 성능 최적화 & 자동화: tree shaking과 같은 최적화 수행, http 요청 수 감소
- Loader : HTML, CSS, JS, Images, Font 등 다양한 파일을 로드할 수 있다.
- Plugin : 번들링 결과물에 대한 후처리 작업을 수행, 최적화 작업 등을 쉽게 할 수 있다.
- Code Splitting : 코드를 적절히 분할하여 초기 로딩 속도를 개선, 필요한 코드만 로딩하여 UX 향상
- 필요할 때만 필요한 스크립트 로드: Dynamic Lodaing & Lazy Loading
- Dependency Issue 해결
- 브라우저를 새로고침하지 않고 변경 사항을 즉시 반영(HMR)

<br>
<br>

## <mark class="pink">📌Webpck 설치</mark>

```powershell
npm i webpack webpack-cli webpack-dev-server -D
```

- webpack : 본체
- webpack-cli : 콘솔에서 실행하는 명령어 담고 있음

<br>
<br>

## <mark class="pink">📌Config 파일들 조정</mark>

**package.json 코드 수정**

```powershell
"scripts": {
   "build": "webpack",
   "dev": "webpack serve"
},
```

**webpack.config.js 만들기**

- [**Entry(엔트리)**](https://webpack.kr/concepts/#entry)
- [**Output(출력)**](https://webpack.kr/concepts/#output)
- [**Loaders(로더)**](https://webpack.kr/concepts/#loaders)
- [**Plugins(플러그인)**](https://webpack.kr/concepts/#plugins)
- [devServer와 devtool 그리고 mode](https://webpack.kr/concepts/#mode)

![Untitled (17)](https://github.com/chlwlstlf/data/assets/63334368/29cb32ed-44d2-4f19-842a-ac12e142df92)

<br>
<br>

### 1. Entry

- 웹팩을 실행할 대상 파일, 그 진입점과 경로 정의
- 메인 JS 파일을 지정
- 그 진입점부터 재귀적으로 빌드
- 트리 쉐이킹 : 의존성 그래프에 포함되지 않은 코드는 빌드 제외
- \_\_dirname : 기본으로 제공하는 변수, root 파일 위치 알려줌

**유형: single이나 array**

```jsx
entry: {
	bundle: patch.resolve(__dirname, 'src/index.js').
}
```

<br>
<br>

### 2. Output

- 웹팩을 실행 후 결과물의 파일 경로
- filename, patch 설정
- **dirname : node에서 제공하는 환경 변수, 현재 파일이 존재하는 폴더명(**filename도 가능)

```jsx
output: {
  path: path.resolve(__dirname, 'dist'),
  filename: '[name][contenthash].js',
  clean: true,
  assetModuleFilename: '[name][ext]',
}
```

```jsx
output: {
  path: path.resolve(__dirname, 'dist'),
  filename: 'bundle.js',
},
```

<br>
<br>

### 3. Loader

- 특정 파일을 해석하고 변환하는 작업 담당
- 모든 리소스를 모듈로 취급하고 그 리소스들을 하나의 파일로 병합 및 압축할 수 있다
- HTML, CSS, 이미지, font 같은 asset 파일들을 웹팩이 인식하고 할 수 있도록 해주는 것을 loader가 한다
- 각 객체에는 적어도 2개의 속성을 입력한다
  `test` : 로더를 적용할 “파일 유형”(CSS, JS 등등)
  `use` : 로더 이름
- 다수의 로더를 적용할 때는 오른쪽에서 왼쪽 순인 관계로 “순서”에 주의해야한다

**🔥 많이 쓰이는 로더**

- files
- CSS
- SASS
- Babel
- TypeScript

```jsx
module: {
	rules: [
		{
			test: /\.scss$/,
			use: ['style-loader', 'css-loader', 'sass-loader'],
		},
		{
			test: /\.js$/,
			exclude: /node_modules/,
			use: {
				loader: 'babel-loader',
				options:{
					presets:['@babel/preset-env'],
				},
			},
		},
	],
}
```

<br>
<br>

### 4. Plugins

- 웹팩 실행시 원하는 추가적인 기능을 추가할 수 있는 옵션

```jsx
plugins: [
	new HtmlWebpackPlugin({
		title: 'Webpack App',
		filename: 'index.html',
		template: 'src/template.html',
	}),
	new BundleAnalyzerPlugin(),
],
```

**🔥 자주 사용하는 플러그인**

- [html-webpack-plugin](https://www.npmjs.com/package/html-webpack-plugin)
- [clean-webpack-plugin](https://www.npmjs.com/package/clean-webpack-plugin)
- [image-webpack-loader](https://www.npmjs.com/package/image-webpack-loader)
- [webpack-bundle-analyzer-plugin](https://www.npmjs.com/package/webpack-bundle-analyzer)

<br>
<br>

### 5. devServer

- 프록시(Proxy) 등 설정

```jsx
devServer: {
	static: {
    directory: path.resolve(__dirname, 'dist'),
  },
  port: 3000,
  open: true,
  hot: true,
  compress: true,
  historyApiFallback: true,
},
```

<br>
<br>

### 6. devtool

- HMR(Hot Module Replacement): 웹팩으로 빌드한 결과물이 웹 애플리케이션에 실시간으로 반영된다
- 소스맵: 압축하여 배포하여 디버깅이 어려운 문제를 해결한 것, 배포된 빌드 파일과 원본 파일을 연결 추적한다

```jsx
devtool: 'source-map',
```

<br>
<br>

### 7. Mode

- `none`
- `development` : 개발 모드
- `production` (default) : 배포 모드, 성능 최적화를 위해 파일 압축 등의 빌드 과정이 추가된다.

```jsx
mode: 'development',
```
