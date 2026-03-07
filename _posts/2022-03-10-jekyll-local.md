---
layout: single
title: "[Jekyll] GitHub 블로그 로컬에서 실행하기"
categories: etc
toc: true
toc_sticky: true
---

# GitHub 블로그 로컬 실행 환경 세팅

Jekyll 기반 GitHub 블로그를 로컬에서 직접 실행하기 위해 환경을 세팅했습니다.  
Ruby 버전 문제부터 gem 의존성 해결까지 겪은 과정을 순서대로 정리합니다.

<br>
<br>

## <mark class="pink">실행 시도 및 에러 해결</mark>

### <mark class="yellow">1단계: jekyll serve 실행 시도</mark>

```bash
bundle exec jekyll serve
```

아래 에러가 발생했습니다.

```
bundler: command not found: jekyll
Install missing gem executables with `bundle install`
```

<br>

### <mark class="yellow">2단계: bundle install → Ruby 버전 충돌</mark>

`bundle install`을 실행하니 이번엔 Ruby 버전 문제가 터졌습니다.

```
ffi-1.17.3-x86_64-darwin requires ruby version >= 3.0, < 4.1.dev,
which is incompatible with the current version, ruby 2.6.10p210
```

시스템에 설치된 Ruby가 **2.6**이었는데, 필요한 gem들이 **Ruby 3.0 이상**을 요구하는 상황이었습니다.

<br>
<br>

## <mark class="pink">rbenv로 Ruby 버전 올리기</mark>

### <mark class="yellow">rbenv란?</mark>

`rbenv`는 프로젝트마다 Ruby 버전을 다르게 설정할 수 있는 버전 관리 도구입니다.  
전역 Ruby를 바꾸지 않고 **특정 디렉토리에서만 원하는 버전**을 사용할 수 있습니다.

<br>

### <mark class="yellow">rbenv 설치</mark>

```bash
brew install rbenv ruby-build
```

<br>

### <mark class="yellow">Ruby 3.3.0 설치</mark>

```bash
eval "$(rbenv init -)"
rbenv install 3.3.0
```

> 내부적으로 컴파일이 이루어지기 때문에 설치에 약 5 ~ 10분이 걸립니다.

<br>

### <mark class="yellow">해당 프로젝트에만 Ruby 버전 고정</mark>

```bash
rbenv local 3.3.0
```

프로젝트 루트에 `.ruby-version` 파일이 생성되고, 이 디렉토리 안에서는 Ruby 3.3.0으로 동작합니다.

<br>

### <mark class="yellow">.ruby-version 파일이란?</mark>

`rbenv local` 명령어를 실행하면 프로젝트 루트에 `.ruby-version` 파일이 자동으로 생성됩니다.

```
3.3.0
```

파일 내용은 단순히 Ruby 버전 문자열 하나입니다.  
rbenv는 이 파일을 감지하면 해당 디렉토리에서 자동으로 지정된 Ruby 버전을 사용합니다.

> 전역(`~/.rbenv/version`)보다 로컬(`.ruby-version`)이 우선 적용됩니다.

`.ruby-version`은 git으로 커밋해두면 팀원 간에 Ruby 버전을 통일하는 데 유용합니다.  
반면, 개인 환경 설정 파일로 관리하고 싶다면 `.gitignore`에 추가해도 됩니다.

<br>
<br>

## <mark class="pink">의존성 설치</mark>

### <mark class="yellow">bundler 및 gem 설치</mark>

```bash
gem install bundler
bundle install
```

<br>

### <mark class="yellow">jemoji 에러 발생</mark>

Jekyll 서버를 실행하자 또 에러가 났습니다.

```
Dependency Error: Yikes! It looks like you don't have jemoji
or one of its dependencies installed.
```

`_config.yml`에 `jemoji` 플러그인이 등록되어 있었지만 `Gemfile`에 누락되어 있었습니다.

`Gemfile`은 Ruby 프로젝트에서 사용할 gem(라이브러리) 목록을 정의하는 파일입니다.  
Node.js의 `package.json`과 같은 역할로, `bundle install`을 실행하면 여기에 적힌 gem들이 설치됩니다.

<br>

### <mark class="yellow">Gemfile에 jemoji 추가</mark>

`Gemfile`에 `jemoji`를 추가하고 다시 설치합니다.

```ruby
source "https://rubygems.org"  # gem을 다운받을 저장소
gemspec                        # .gemspec 파일의 의존성도 포함
gem "webrick", "~> 1.7"        # 로컬 서버 실행에 필요한 gem
gem "jemoji"                   # 이모지 플러그인 (추가)
```

```bash
bundle install
```

<br>
<br>

## <mark class="pink">로컬 서버 실행</mark>

```bash
bundle exec jekyll serve
```

아래와 같이 출력되면 정상적으로 실행된 것입니다.

```
    Server address: http://127.0.0.1:4000
  Server running... press ctrl-c to stop.
```

`http://localhost:4000`으로 접속하면 블로그가 로컬에서 실행됩니다.

<br>
<br>

## <mark class="pink">package.json에 명령어 추가</mark>

매번 `eval "$(rbenv init -)"` 을 입력하기 번거로우므로, `package.json`의 `scripts`에 등록해두면 편리합니다.

```json
"scripts": {
  "dev": "eval \"$(rbenv init -)\" && bundle exec jekyll serve --livereload"
}
```

이후에는 아래 명령어 하나로 실행됩니다.

```bash
npm run dev
```

`--livereload` 옵션을 추가하면 파일을 수정할 때마다 브라우저가 자동으로 새로고침됩니다.

<br>
<br>

## <mark class="pink">정리</mark>

| 문제                                | 해결                                                  |
| ----------------------------------- | ----------------------------------------------------- |
| `jekyll` 명령어를 찾을 수 없음      | `bundle install` 실행                                 |
| Ruby 버전 incompatible (2.6 → 3.0+) | `rbenv install 3.3.0` → `rbenv local 3.3.0`           |
| jemoji gem 없음                     | `Gemfile`에 `gem "jemoji"` 추가 후 `bundle install`   |
| 매번 rbenv 초기화 번거로움          | `package.json` scripts에 등록 후 `npm run dev`로 실행 |
