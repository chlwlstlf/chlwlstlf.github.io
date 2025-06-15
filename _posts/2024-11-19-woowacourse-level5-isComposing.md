---
layout: single
title: "[우테코] Level5 onKeyDown 이벤트가 두 번 발생하는 이유"
categories: woowacourse
toc: true
toc_sticky: true
---

# 키워드 입력하고 Enter 키를 눌렀을 때 두 번 입력되는 오류

## <mark class="pink">🔥Enter 키로 키워드 생성하기</mark>

- 키워드 엔터로 추가, 생성된 키워드 클릭으로 삭제하는 기능을 만들었다.
- title 속성을 추가하여 명확한 동작을 알려주었다.

![구현1](https://github.com/user-attachments/assets/dd7e17db-c6b3-4e2a-9978-3d10f091fe34)

```tsx
import React from "react";
import { useState } from "react";
import Icon from "@/components/common/icon/Icon";
import { Input } from "@/components/common/input/Input";
import * as S from "@/components/common/keyword/Keyword.style";

interface KeywordProps {
  currentKeywords: string[];
  onKeywordsChange: (keywords: string[]) => void;
  error: boolean;
}

const Keyword = ({
  currentKeywords,
  onKeywordsChange,
  error,
}: KeywordProps) => {
  const [keyword, setKeyword] = useState<string>("");

  const removeKeyword = (index: number) => {
    const updatedKeywords = currentKeywords.filter((_, i) => i !== index);
    onKeywordsChange(updatedKeywords);
  };

  const handleInputChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    setKeyword(e.target.value);
  };

  const handleKeyDown = (e: React.KeyboardEvent<HTMLInputElement>) => {
    if (e.key === "Enter") {
      e.preventDefault();

      const trimmedKeyword = keyword.trim();

      if (trimmedKeyword === "") return;

      if (
        trimmedKeyword.includes("⠀") ||
        currentKeywords.includes(trimmedKeyword)
      ) {
        setKeyword("");
        return;
      }

      onKeywordsChange([...currentKeywords, trimmedKeyword]);
      setKeyword("");
    }
  };

  return (
    <S.KeywordContainer>
      {currentKeywords.length !== 0 && (
        <S.KeywordLabelContainer>
          {currentKeywords.map((keyword, index) => (
            <S.KeywordButton
              key={keyword}
              onClick={() => removeKeyword(index)}
              title="키워드를 클릭하면 삭제돼요"
            >
              {keyword}
              <Icon kind="delete" size="2.2rem" />
            </S.KeywordButton>
          ))}
        </S.KeywordLabelContainer>
      )}
      <Input
        type="text"
        placeholder="Enter키를 누르면 키워드가 생성돼요"
        value={keyword}
        showCharCount={true}
        maxLength={20}
        onChange={handleInputChange}
        onKeyDown={handleKeyDown}
        error={error}
      />
    </S.KeywordContainer>
  );
};

export default Keyword;
```

<br>
<br>

## <mark class="pink">🔥[ 문제상황 ] 마지막 글자만 또 추가됨</mark>

MacOS+Chrome 환경에서 마지막 글자에 밑줄이 있으면 그 글자만 또 추가가 되는 오류가 발생했다.

<video controls>
  <source src="https://github.com/user-attachments/assets/e34c69f4-0cb6-49d8-bd18-14dbdd32d2ac" type="video/mp4">
</video>

![문제상황2](https://github.com/user-attachments/assets/6a206ba4-2e16-4960-b62a-7475fc2bb24b)

<br>
<br>

## <mark class="pink">🔥[ 옛날 해결방법 ] onKeyPress로 변경</mark>

**<mark class="yellow">onKeyPress로 변경?</mark>**

onKeyDown: keycode 값 - 한/영, Shift, Backspace 등 인식 가능  
onKeyPress: ASCII 값 - 한/영, Shift, Backspace 등 인식 불가

onKeyDown 대신 onKeyPress를 사용하면 해결할 수 있다는데 바꿔야 하나?

그건 안 된다. Shift와 Backspace를 인식해야하기 때문이다.

<br>

**<mark class="yellow">onKeyPress는 deprecated되었다</mark>**

onKeyPress는 이벤트는 공식적으로 deprecated되었다. deprecated란 해당 기능을 지원하는 곳에서 성능 감소 또는 업그레이드된 기능 출시 등의 다양한 이유로 인해 공식적으로 사용을 권장하지 않는단 뜻이다. 일부 브라우저는 여전히 지원하지만 관련 웹 표준에서 이미 제거되었거나 삭제 중이거나 호환성 목적으로만 유지되었을 수 있기에 사용을 지양해야한다.

![해결방안1-1](https://github.com/user-attachments/assets/fdd9ba61-4ced-478d-852b-ce50eaa4992d)

따라서 onKeyDown을 사용하면서 해결해야 한다.

<br>
<br>

## <mark class="pink">🔥[ 원인 ] IME와 조합글자</mark>

한글 입력 시 이벤트가 두 번 발생하는 이유는 **IME(Input Method Editor)**라는 입력 방식의 특성과 연관이 있다.

한글은 조합형 언어이기 때문에 하나의 글자를 입력할 때 여러 개의 키 입력이 조합되는 과정이 필요하다. 한글처럼 조합 과정이 필요한 언어는 IME를 통해 조합 상태를 관리하므로, 조합 이벤트(`composition`)가 추가로 발생한다.

<br>
<br>

## <mark class="pink">🔥[ 실험 ] 다양한 OS와 브라우저에서의 IME</mark>

왜 MacOS+Chrome 환경에서만 이런 오류가 생기는지 IME를 어떻게 처리해서 문제가 발생하는지 궁금했다. 추가로 크로스 브라우징 이슈를 해결해보고 싶어 **다양한 OS와 브라우저 환경에서 실험해보았다.**

다르 블로그에 샤라웃 당했다. 맥북 유저 다르 감사합니다🙇‍♀️
<img src="https://github.com/user-attachments/assets/3b9b41c9-7805-4cb3-8b7e-294570b1ff27" style="border: 1px solid #ccc; border-radius: 10px;" />

<br>

**<mark class="yellow">1. Window + Chrome</mark>**

우선 나의 개발 환경이다. 이때는 두 번 입력되는 오류가 뜨지 않았다.

![window+chrome](https://velog.velcdn.com/images/darr/post/cfb1ad40-048c-4afe-b1bd-8fad5d83d49b/image.png)

`handleKeyDown` 이벤트 안에서 `keyword`, `e.nativeEvent.isComposing`, `e.key` 콘솔을 찍어보았다.

`keyword`: 유저가 입력하는 Input 값 (React.state에 저장된 값)  
`e.nativeEvent.isComposing`: 현재 조합 중인지에 대한 여부  
`e.key`: 유저가 타이핑 값

조합 중일 때 Enter를 누르면 `Process`로 한 번, `Enter`로 한 번 실행된다. 한글 조합이 완료되면 input의 값이 갱신되고, React는 이를 새로운 입력으로 감지해 onKeyDown이 한 번 더 실행되게 된다.

여기서 isComposing이 `true`일 때는 e.key 값이 타이핑한 값이 아니라 `Process`임을 기억하고 맥북 환경으로 넘어가 보자.

<br>
<br>

**<mark class="yellow">2. MacOS + Chrome</mark>**

문제가 발생했던 환경이다.

![MacOS+Chrome](https://github.com/user-attachments/assets/b36e51ed-53b9-482f-bec4-b70242eb9170)

맥+Chrome에서는 `e.key`의 값이 **타이핑한 값**이다. 마지막에 Enter가 두 번 실행되는데 한 번은 "안녕"으로 두 번째는 "녕"으로 실행된다.

윈도우에서도 조합 중일 때 Enter를 누르면 두 번 실행되지만 첫 번째는 isComposing이 `true`라 `Process` 키를 누른 것으로 인식하고, 한 번 더 실행됐을 때 e.key가 `Enter`가 된다.

하지만 맥은 e.key가 타이핑한 값이기 때문에 **두 번 모두** `Enter`로 인식하고 동작하여 오류가 생긴다. "녕"은 실제 사용자가 따로 입력한 것이 아니라 조합 중이던 문자가 다시 인식되는 것이다.

<br>
<br>

**<mark class="yellow">3. MacOS + Safari</mark>**

Safari에서는 해당 문제가 생기지 않았다.

![MacOS+safari](https://github.com/user-attachments/assets/5f2f79b2-0bec-448f-ac4d-2ceeec800707)

Safari는 조합 중 상태를 감지하지 않고 `isComposing` 값을 `false`로 유지한다. 따라서 이벤트도 한 번만 실행된다.

<br>
<br>

**<mark class="yellow">4. MacOS + Firefox</mark>**

Firefox에서도 해당 문제가 생기지 않았다.

![MacOS+firefox](https://github.com/user-attachments/assets/6a5537e6-65fa-430c-a13a-41ed366b8998)

여기서도 조합 중이면 e.key가 `Process`로 출력된다. Window+Chrome과 이 부분은 비슷하지만 다른 점이 있다.  
Enter 키를 누르면 먼저 `compositionend`가 발생하여 조합이 종료되고, 그 뒤에 `keydown` 이벤트가 `Enter` 키로 한 번만 발생한다. 이때 `isComposing`은 false 이고 중복 이벤트는 발생하지 않는다.

<br>
<br>

## <mark class="pink">🔥[ 해결방안 ] 조합 중이 아닐 때만 이벤트 실행</mark>

**<mark class="yellow">KeyboardEvent.isComposing?</mark>**

`KeyboardEvent.isComposing`은 입력한 문자가 조합문자인지 아닌지를 판단한다. 한글은 자음과 모음의 조합으로 한 음절이 만들어지기 때문에 조합문자이고, 영어는 조합문자가 아니다.

한글을 입력할 때 자세히 보면 입력 중인 글자 바로 아래에 검은 **밑줄이 생기는 경우**가 있는데, 이 밑줄이 보이는 상황에서 `Enter`키를 입력하면 이벤트가 2번 호출되는 이슈가 있다.

그 이유는 React의 `onKeyDown`은 DOM 이벤트의 래퍼로 동작하는데 IME가 조합 완료 후 DOM의 value를 갱신하면, React는 이를 **새로운 상태**로 인식하고 `onKeyDown`을 다시 호출한다. 이로 인해 한 번 더 Enter 키 입력이 발생한 것처럼 보이는 현상이 나타난다.

<br>

**<mark class="yellow">React에서 isComposing 사용</mark>**

Vanilla JavaScript에서는 네이티브 DOM 이벤트 객체를 바로 사용할 수 있어 `event.isComposing`로 코드를 작성할 수 있다.

하지만 React에는 `Synthetic Event`라는 것이 있다. `Synthetic Event`란 React의 이벤트를 브라우저의 네이티브 DOM 이벤트에 래핑한 것으로 React의 이벤트 시스템(이벤트 풀링, 크로스 브라우저 호환성 등)에 최적화되어 있다. 또 React가 자체적으로 관리하므로 이벤트 핸들러는 모든 브라우저에서 동일하게 작동한다.

`Synthetic Event`는 React가 제공하는 인터페이스에 따라 동작하며, 네이티브 DOM 이벤트가 제공하는 모든 속성을 **직접 노출하지 않을 수 있다**. 특정 속성이나 동작이 Synthetic Event에서 노출되지 않으면 nativeEvent를 통해 원래 DOM 이벤트에 접근해야 한다.

`isComposing`도 노출되지 않은 속성 중 하나여서 `event.nativeEvent.isComposing`으로 `isComposing`에 접근할 수 있다.

<br>

**<mark class="yellow">isComposing이 false일 때만 이벤트 실행</mark>**

onKeyDown 이벤트는 조합 중 상태와 조합 완료 상태에서 모두 발생할 수 있다. IME(입력기)의 조합 중 상태에서 이벤트가 중복 실행되거나, 잘못된 중간 값이 처리되는 문제를 방지하기 위해 `isComposing === false`일 때만 이벤트를 실행해야한다.

```tsx
const handleKeyDown = (e: React.KeyboardEvent<HTMLInputElement>) => {
  if (e.key === "Enter") {
    e.preventDefault();

    const trimmedKeyword = keyword.trim();

    if (trimmedKeyword === "") return;

    if (
      trimmedKeyword.includes("⠀") ||
      currentKeywords.includes(trimmedKeyword)
    ) {
      setKeyword("");
      return;
    }

    // Enter 키 누름 && 조합 중이 아닐 때만 키워드 추가
    if (e.nativeEvent.isComposing === false) {
      onKeywordsChange([...currentKeywords, trimmedKeyword]);
      setKeyword("");
    }
  }
};
```

<br>

**➕ [최종] MacOS + Chrome에서 20자를 넘겼을 때**

그 전에는 21번째 글자도 함께 keyword에 들어갔었는데 false일 때 if문을 추가한 후에는 21번째 글자가 조합 중일 때 20자까지만 keyword로 인식된다.

![MacOS+Chrome 긴글자](https://github.com/user-attachments/assets/19c4dcfd-ca1e-4428-8ca0-4b598de15738)

<br>
<br>

**참고**  
[한글-입력-시-마지막-음절이-중복-입력되는-경우-함수가-두-번-실행되는-경우](https://velog.io/@corinthionia/JS-keydown%EC%97%90%EC%84%9C-%ED%95%9C%EA%B8%80-%EC%9E%85%EB%A0%A5-%EC%8B%9C-%EB%A7%88%EC%A7%80%EB%A7%89-%EC%9D%8C%EC%A0%88%EC%9D%B4-%EC%A4%91%EB%B3%B5-%EC%9E%85%EB%A0%A5%EB%90%98%EB%8A%94-%EA%B2%BD%EC%9A%B0-%ED%95%A8%EC%88%98%EA%B0%80-%EB%91%90-%EB%B2%88-%EC%8B%A4%ED%96%89%EB%90%98%EB%8A%94-%EA%B2%BD%EC%9A%B0)  
[onKeyPress is deprecated!](https://velog.io/@diso592/onKeyPress-is-deprecated)
