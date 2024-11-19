---
layout: single
title: "[우테코]Level5 onKeyDown 이벤트가 두 번 발생하는 이유"
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

한글 입력 시 이벤트가 두 번 발생하는 이유는 **IME(Input Method Editor)**라는 입력 방식의 특성과 연관이 있다. 한글은 조합형 언어로, 하나의 글자를 입력할 때 여러 개의 키 입력이 조합되는 과정이 필요하다. 이 과정에서 브라우저는 입력 상태를 처리하기 위해 **추가적인 이벤트**를 발생시킨다.

한글처럼 조합 과정이 필요한 언어는 IME를 통해 조합 상태를 관리하므로, 조합 이벤트(`composition`)가 추가로 발생한다.

<br>
<br>

## <mark class="pink">🔥[ 해결방안 ] isComposing이 아닐 때 이벤트 실행</mark>

**<mark class="yellow">KeyboardEvent.isComposing?</mark>**

`KeyboardEvent.isComposing`은 입력한 문자가 조합문자인지 아닌지를 판단한다. 한글은 자음과 모음의 조합으로 한 음절이 만들어지기 때문에 조합문자이고, 영어는 조합문자가 아니다.

한글을 입력할 때 자세히 보면 입력 중인 글자 바로 아래에 검은 밑줄이 생기는 경우가 있는데, 이 밑줄이 보이는 상황에서 `Enter`키를 입력하면 이벤트가 2번 호출되는 이슈가 있다.

그 이유는 React의 onKeyDown은 DOM 이벤트의 래퍼로 동작하는데 IME가 조합 완료 후 DOM의 value를 갱신하면, React는 이를 새로운 상태로 인식하고 onKeyDown을 다시 호출한다. 이로 인해 한 번 더 Enter 키 입력이 발생한 것처럼 보이는 현상이 나타난다.

<br>

**<mark class="yellow">React에서 isComposing 사용</mark>**

Vanilla JavaScript에서는 네이티브 DOM 이벤트 객체를 바로 사용할 수 있어 `event.isComposing`로 코드를 작성할 수 있다.

하지만 React에는 Synthetic Event라는 것이 있다. Synthetic Event란 React의 이벤트를 브라우저의 네이티브 DOM 이벤트에 래핑한 것으로 React의 이벤트 시스템(이벤트 풀링, 크로스 브라우저 호환성 등)에 최적화되어 있다. 또 React가 자체적으로 관리하므로 이벤트 핸들러는 모든 브라우저에서 동일하게 작동한다.

Synthetic Event는 React가 제공하는 인터페이스에 따라 동작하며, 네이티브 DOM 이벤트가 제공하는 모든 속성을 직접 노출하지 않을 수 있다. 특정 속성이나 동작이 Synthetic Event에서 노출되지 않으면 nativeEvent를 통해 원래 DOM 이벤트에 접근해야 한다.

isComposing도 노출되지 않은 속성 중 하나여서 `event.nativeEvent.isComposing`으로 isComposing에 접근할 수 있다.

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

    // 조합 중이 아닐 때만 키워드 추가
    if (e.nativeEvent.isComposing === false) {
      onKeywordsChange([...currentKeywords, trimmedKeyword]);
      setKeyword("");
    }
  }
};
```

<br>
<br>

## <mark class="pink">🔥다양한 OS와 브라우저에서의 IME</mark>

**<mark class="yellow">1. Window + Chrome</mark>**

우선 나의 개발 환경이다. 이때는 두 번 입력되는 오류가 뜨지 않았다.

handleKeyDown 이벤트 안에서 isComposing과 누른 key, handleInputChange 이벤트 안에서 value를 출력해보았다.

![window+chrome](https://github.com/user-attachments/assets/acc6340b-62eb-475b-9b56-69992a146606)

Windows의 IME(입력기)는 조합 중 상태에서 입력된 키를 처리할 때 key 속성을 "Process"로 반환한다. 조합 상태가 없는 영어는 해당 알파벳이 출력된다. "Process"는 IME가 키 입력을 처리 중이며, 해당 키의 의미를 아직 최종적으로 결정하지 않았음을 나타내는 값이다.

<br>

"안녕"을 다 입력하고 Enter 키를 누르면 Process로 한 번, Enter로 또 한 번 handleKeyDown 이벤트가 실행된다.

`isComposing: true, key: "Process"`

- 조합 중 상태에서 Enter 키 입력이 감지되었다.
- IME는 아직 조합 상태를 유지하고 있으며, 최종 값을 확정하지 않았다.

`isComposing: false, key: "Enter"`

- 조합이 완료되었고, 최종 값("안녕")이 DOM에 반영되었다.
- Enter 키가 조합 완료 이후의 동작으로 다시 감지되었다.

출력 결과

```
안녕
isComposing true key Process
isComposing false key Enter
```

<br>
<br>

**<mark class="yellow">2. MacOS + Chrome</mark>**

문제가 발생했던 환경이다.

![MacOS+Chrome](https://github.com/user-attachments/assets/b36e51ed-53b9-482f-bec4-b70242eb9170)

**왜 Enter 이후 IME의 "녕"이 다시 e.target.value에 들어가게 되는가?**

1\. Enter 키 이벤트와 IME의 동작 순서

- IME는 한글 조합을 유지하다가 compositionend 이벤트를 통해 최종 값을 DOM에 반영한다.
- onKeyDown 이벤트에서 Enter 키가 처리될 때, 조합이 완료되지 않았기 때문에 IME가 **중간값("녕")**을 DOM에 남겨두게 된다.

2\. React의 상태 초기화

- setKeyword("")를 호출하면서 keyword는 빈 문자열로 초기화된다.
- 하지만 React는 DOM에서 발생하는 이벤트(onKeyDown 등)를 계속 감지하며, 이때 DOM의 value가 다시 e.target.value로 전달된다.

3\. IME의 조합 중 값이 DOM에 다시 반영

- setKeyword("")로 상태를 초기화해도, IME는 여전히 조합 중 상태를 유지하면서 조합 값을 DOM에 반영한다.
- 조합이 끝나지 않았기 때문에 "녕"이 다시 DOM의 value로 나타나고, React는 이를 감지하여 onChange 또는 onKeyDown에서 다시 상태로 저장하게 된다.

**false일 때만 처리한 후 20자를 넘겼을 때**

그 전에는 21번째 글자도 함께 keyword에 들어갔었는데 false일 때 if문을 추가한 후에는 21번째 글자가 조합 중일 때 20자까지만 keyword로 인식된다.

![MacOS+Chrome 긴글자](https://github.com/user-attachments/assets/19c4dcfd-ca1e-4428-8ca0-4b598de15738)

<br>
<br>

**<mark class="yellow">3. MacOS + Safari</mark>**

Safari에서는 해당 문제가 생기지 않았다.

Safari는 조합 중 상태를 감지하지 않으며, isComposing 값을 false로 유지한다. 따라서 이벤트도 한 번만 실행된다.

![MacOS+safari](https://github.com/user-attachments/assets/5f2f79b2-0bec-448f-ac4d-2ceeec800707)

<br>
<br>

**<mark class="yellow">4. MacOS + Firefox</mark>**

Firefox에서도 해당 문제가 생기지 않았다.

![MacOS+firefox](https://github.com/user-attachments/assets/6a5537e6-65fa-430c-a13a-41ed366b8998)

여기서도 조합 중이면 key가 Process로 출력된다. Window+Chrome과 매우 비슷하게 작동한다. isComposing이 false일 때만 이벤트가 실행된다.

<br>
<br>

**참고**  
[한글-입력-시-마지막-음절이-중복-입력되는-경우-함수가-두-번-실행되는-경우](https://velog.io/@corinthionia/JS-keydown%EC%97%90%EC%84%9C-%ED%95%9C%EA%B8%80-%EC%9E%85%EB%A0%A5-%EC%8B%9C-%EB%A7%88%EC%A7%80%EB%A7%89-%EC%9D%8C%EC%A0%88%EC%9D%B4-%EC%A4%91%EB%B3%B5-%EC%9E%85%EB%A0%A5%EB%90%98%EB%8A%94-%EA%B2%BD%EC%9A%B0-%ED%95%A8%EC%88%98%EA%B0%80-%EB%91%90-%EB%B2%88-%EC%8B%A4%ED%96%89%EB%90%98%EB%8A%94-%EA%B2%BD%EC%9A%B0)  
[onKeyPress is deprecated!](https://velog.io/@diso592/onKeyPress-is-deprecated)
