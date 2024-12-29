---
layout: single
title: "[우테코]Level1 DOM, BOM, Event, CSS"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션2 LMS 강의 자료2

## <mark class="pink">📚DOM</mark>

**<mark class="yellow">DOM : Document Object Model</mark>**

문서 객체 모델(Document Object Model)은 HTML, XML 문서의 프로그래밍 interface입니다. DOM은 문서의 구조화된 표현을 제공하며 **프로그래밍 언어가 DOM 구조에 접근할 수 있는 방법을 제공**하여 문서 구조 및 스타일, 내용 등을 변경할 수 있게 도와준다.

DOM은 HTML을 위한 API이면서 HTML을 탐색할 수 있고 HTML의 구조를 바꿀 수도 있다.

<br>

**<mark class="yellow">DOM API</mark>**

**설명**  
DOM이 제공하는 API를 이용해서 검색어에 해당하는 문자열을 보다 쉽게 가져올 수 있다.

<br>

**DOM 객체를 찾는 메서드**

- getElement, querySelector: 엘리먼트 한 개만 반환
- getElements, querySelectorAll: 엘리먼트 여러개 반환

<br>

**DOM 객체를 찾는 키워드**

예시

```html
<input id="search" class="search-input-style" />
```

1\. tag

```js
document.getElementsByTagName("input");
```

---

2\. id
속도가 가장 빠름

```js
document, getElementById("search");
```

---

3\. className

```js
document.getElementsByClassName("search-input-style");
```

---

4\. cssSelector

일치하는 **첫번째** 엘리먼트

```js
document.querySelector(".search-input-style");
```

일치하는 **모든** 엘리먼트

```js
document.querySelectorAll(".search-input-style");
```

<br>

**<mark class="yellow">추가 학습</mark>**

**1\. 내가 작성한 HTML 코드가 DOM인가?**  
아니다. 내가 작성한 코드가 브라우저에 의해 파싱되면 DOM이 된다.

**2\. 페이지 View Source가 DOM인가?**  
아니다 View Source는 그 페이지를 이루고 있는 HTML이며 내가 작성한 HTML과 같다.

**3\. 개발자 도구에서 보이는 코드가 DOM인가?**  
아니다. DOM과 가장 가까운 근사치를 제공한다. 하지만 개발자 도구에서는 DOM에 없는 추가적인 정보를 포함한다. CSS의 가상 요소 `::before`와 `::after` 선택자는 DOM의 일부가 아님에도 개발자 도구에서 확인할 수 있다. 이 가상 요소는 DOM의 일부가 아니기 때문에 자바스크립트에 의해 수정될 수 없다.

**4\. 브라우저에서 보이는 것이 DOM인가?**  
아니다. 브라우저에 보이는 것은 렌더 트리로 DOM과 CSSOM의 조합이다. DOM은 `display: none` 태그를 포함하지만 렌더 트리는 포함하지 않는다.

**5\. 브라우저가 HTML을 수정한다.**  
내가 작성한 HTML에 실수가 있으면 브라우저가 이를 대신 고친다. `<html>`이나 `<body>` 태그 같은 경우 항상 존재해야 하는데 실수로 작성하지 않아도 브라우저에 의해 DOM에 항상 존재한다. 태그의 닫는 짝이 없거나 필수 중첩 태그(`<ul`과 `<li>`)가 빠진 경우 DOM이 자동으로 추가해준다.

**6\. Javascript로 DOM을 조작하는 경우 HTML과 달라진다.**

아래 HTML을 JS로 조작하면 내가 작성한 HTML과 DOM이 달라진다.

```html
<div id="container"></div>
```

```js
const container = document.getElementById("container");
container.innerHTML = "New Content!";
```

---

DOM 결과(개발자 도구에서 확인)

```html
<div id="container">New Content!</div>
```

<br>
<br>

## <mark class="pink">📚BOM</mark>

**<mark class="yellow">BOM : Browser Object Model</mark>**

BOM은 웹 브라우저 환경의 다양한 기능을 객체처럼 다루는 모델이다.

대부분의 브라우저에서 구현은 되어있지만, 정의된 표준이 없어 브라우저 제작사마다 세부사항이 다르고 다소 한정적이라는 특징이 있다.

BOM의 역할은 웹 브라우저의 버튼, URL 주소 입력 창, 타이틀 바 등 웹브라우저 윈도우 및 웹페이지의 일부분을 제어할 수 있게끔 하는 것이다. window 객체를 통해 접근이 가능하다.

<br>

**<mark class="yellow">대표적인 BOM 객체</mark>**

브라우저 전체를 담당하는 게 **window** 객체이고, 웹사이트만 담당하는 게 **document** 객체이다. document 객체도 window 객체 안에 있다.

window 객체는 BOM 객체이지만 그 안의 document 객체는 DOM이고 나머지 객체가 BOM이다. BOM은 DOM을 포함하는 것이 아니라, window 객체를 통해 DOM을 제어할 수 있는 기능을 제공하는 것이다.

**window**는 브라우저 창 객체이며 Global Context이다. `window.`을 생략해도 되지만 다른 함수와 혼돈할 수 있기 때문에 보통 붙여준다.

<br>

**document 외 window 안의 객체들**

- **screen**: 사용자 환경의 디스플레이 정보 객체
- **location**: 현재 페이지의 url을 다루는 객체
- **navigator**: 웹브라우저 및 브라우저 환경 정보 객체
- **history**: 현재의 브라우저가 접근했던 URL history

<div class="blue-box">
  <p>
    <b>💡Tip</b>
    <div>beforeunload 이벤트를 사용하면 페이지를 벗어날 때 확인 메세지를 표시할 수 있다.</div>
    <div>단, 크롬에서는 경고창에 문구가 나타나지 않습니다.</div>
    <img src="https://techcourse-storage.s3.ap-northeast-2.amazonaws.com/2020-04-21T15:31:35.512%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202020-04-21%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%203.31.25.png" />
  </p>
</div>

```js
window.onbeforeunload = function () {
  return "작성 중인 메세지가 있습니다.";
};
```

<br>
<br>

## <mark class="pink">📚Event</mark>

**<mark class="yellow">이벤트 종류</mark>**

**1\. 사용자 인터페이스 이벤트**

브라우저 창을 사용할 때 발생하는 이벤트

- load: 웹 페이지의 로드가 완료되었을 때
- unload: 웹 페이지가 unload 될 때 (새로운 페이지를 요청한 경우)
- error: 브라우저가 자바스크립트 오류를 만났거나 요청한 자원이 존재하지 않는 경우
- resize: 브라우저 창의 크기를 조정했을 때
- scroll: 사용자가 페이지를 위아래로 스크롤 할 때

<br>

**2\. 키보드 이벤트**

사용자가 키보드를 이용할 때 발생하는 이벤트

- keydown: 사용자가 키를 처음 눌렀을 때

<video controls>
  <source src="https://github.com/user-attachments/assets/d86bc7cc-2ba1-4553-9af2-b695b449f50b" type="video/mp4">
</video>

- keyup: 사용자가 키를 뗄 때

<video controls>
  <source src="https://github.com/user-attachments/assets/e4ced8c2-8445-477f-bf40-9470fcec4fdc" type="video/mp4">
</video>

- keypress(Deprecated): 사용자가 키를 눌렀다가 뗴어서 문자가 화면에 나타나게 되면 발생한다. 화살표 키를 누를 때 keydown 이벤트는 발생하지만 이 이벤트는 발생하지 않는다. 사용자가 키를 계속 누르고 있으면 이벤트가 반복적으로 발생한다.

<br>

**3\. 마우스 이벤트**

마우스를 움직이거나 버튼을 클릭했을 때

- click: 마우스를 클릭했을 때
- dblclick: 마우스를 더블 클릭했을 때
- mousedown: 마우스를 누르고 있을 때
- mouseup: 요소 위에서 누르고 있던 마우스를 뗄 때
- mousemove: 마우스를 움직일 때(터치스크린에서 동작x)
- mouseover: 요소 위로 마우스를 움직였을 때(터치스크린에서 동작x)
- mouseout: 요소 바깥으로 마우스를 움직였을 때(터치스크린에서 동작x)

<br>

**4\. focus와 blur 이벤트**

사용자가 상호작용을 할 수 있는 링크나 폼 요소 같은 HTML 요소는 포커스를 받을 수 있다.  
이런 요소들은 자신이 포커스를 갖거나 잃을 떄 이벤트를 발생시킨다.

사용자가 폼의 요소들과 상호작용을 할 때 도움말이나 피드백을 제공하고자 하는 경우  
사용자가 어느 한 요소에서 다른 요소로 이동했을 때 유효성 검사를 수행할 필요가 있는 경우

- focus/focusin: 요소가 포커스를 얻었을 때
- blur/focusout: 요소가 포커스를 잃었을 때

<br>

**<mark class="yellow">이벤트 핸들러</mark>**

**소개**  
사용자가 웹 페이지의 HTML과 상호작용할 때 이벤트를 핸들링하도록 프로그래밍할 수 있다.

**종류**  
1\. HTML 이벤트 핸들러  
2\. 전통적인 DOM 이벤트 핸들러  
3\. 이벤트 리스너(이 방법으로 보통 이벤트를 바인딩함)

<br>

**<mark class="yellow">이벤트 리스너</mark>**

![이벤트 리스너](https://user-images.githubusercontent.com/50367798/61576496-ccca8c80-ab15-11e9-8835-caf2b240f65c.png)

```js
function handleButtonClick() {
  // 이벤트 처리 로직을 구현
}

// 추가 버튼 DOM을 찾아오기
const $addButton = document.querySelector("#add-button");

// 클릭 이벤트가 발생했을 때 handleButtonClick함수가 실행되도록 바인딩
$addButton.addEventListener("click", handleButtonClick);
```

<br>

**1\. 함수 참조**  
클릭 시 handleButtonClick이 실행된다. 함수 자체를 이벤트 핸들러로 등록했기 때문이다.  
event는 브라우저가 자동으로 전달하지만, 함수에서 이를 선언하지 않으면 무시된다.

```js
function handleButtonClick(event) {
  console.log(event.type);
}

$addButton.addEventListener("click", handleButtonClick);
```

<br>

**2\. 함수 호출(틀린 코드)**  
코드를 실행하는 시점에서 handleButtonClick()이 호출되고, 반환값이 이벤트 핸들러로 설정된다.

```js
function handleButtonClick(event) {
  console.log(event.type);
}

$addButton.addEventListener("click", handleButtonClick());
```

<br>

**3\. 매개변수 전달**  
이벤트 핸들러가 기본적으로 클릭 이벤트 객체를 자동으로 전달하기 때문에 `() =>` 없이 함수 참조를 바로 전달할 수 없다.

```js
function handleButtonClick(arg) {
  console.log(arg);
}

$addButton.addEventListener("click", () => handleButtonClick("some argument"));
```

```js
function handleButtonClick(event, arg) {
  console.log(event.type, arg);
}

$addButton.addEventListener("click", (event) =>
  handleButtonClick(event, "some argument")
);
```

<br>

**<mark class="yellow">이벤트의 흐름과 제어</mark>**

![이벤트 흐름](https://user-images.githubusercontent.com/50367798/61577456-7f085100-ab22-11e9-9548-f302b2588bd2.jpg)

**이벤트 버블링**  
이벤트가 사용자의 동작에 직접적으로 영향을 받은 노드로부터 바깥쪽으로 전파되어 나가는 방식

**이벤트 캡쳐링**  
이벤트가 가장 바깥쪽의 노드로부터 시작해서 안쪽으로 전파되어 들어오는 방식

<br>

**<mark class="yellow">Event 객체</mark>**

이벤트가 발생했을 때 이벤트를 발생시킨 요소와 발생한 이벤트에 대한 정보를 제공한다. 이벤트 객체가 제공해주는 메서드를 이용하면 이벤트 흐름 관련 문제를 미리 예방할 수 있다.

**Event 객체가 제공하는 정보**  
이벤트 객체는 이벤트 리스너로 지정된 함수에 자동으로 전달된다.

- 이벤트를 발생시킨 요소
- keypress 이벤트가 어떤 키에 의해 발생했는지에 대한 정보
- 사용자가 어떤 요소를 클릭해서 click 이벤트가 발생했는지에 대한 정보

**속성**

|  속성  |                          목적                          |
| :----: | :----------------------------------------------------: |
| target | 이벤트가 발생한 요소(사용자가 의도한 가장 명확한 요소) |
|  type  |                  발생한 이벤트의 종류                  |

**메서드**

|      메서드       |                                                                                                            목적                                                                                                            |
| :---------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| preventDefault()  | 이벤트의 기본 동작을 **취소**한다.<br>링크를 클릭하거나 폼을 제출하는 것과 같은 몇 가지 이벤트들은 사용자에게 다른 페이지를 보여준다. 이런 요소들의 기본 동작을 중단할 때(링크 제출해도 같은 페이지를 유지할 때) 사용한다. |
| stopPropagation() |                                                             이벤트의 캡처링이나 버블링을 중단한다.<br>이벤트가 부모 요소로 버블링되는 것을 중단할 때 사용한다.                                                             |

<br>

**<mark class="yellow">이벤트 위임</mark>** |

<br>
<br>

**참고**

[DOM이란-무엇인가](https://velog.io/@godori/DOM%EC%9D%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)
