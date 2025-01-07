---
layout: single
title: "[우테코]Level1 로또 게임 2단계"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션2 🎱로또 게임 2단계

## <mark class="pink">🔥2단계 구현 사항</mark>

[2단계 - 웹 기반 로또 게임](https://github.com/woowacourse/javascript-lotto/pull/313){: .btn .btn--primary}

### <mark class="yellow">1. 정적인 부분은 html에 미리 구현</mark>

**1\. main\_\_title-wrapper**

title은 화면에 진입했을 때 가장 먼저 보이는 부분이고 동적으로 바뀌지 않기 때문에 html에 코드를 짰다.

![1](https://github.com/user-attachments/assets/ec40034e-b522-4ae2-a3b9-50daa00058dd)

**index.html**

```html
<div class="main__title-wrapper">
  <h2 class="main__title">🎱 내 번호 당첨 확인 🎱</h2>
</div>
```

<br>

**2\. purchase-form**

구입 금액 입력 부분도 첫 화면에 바로 보이는 부분이기 때문에 html에 모두 구현하였다.

![2](https://github.com/user-attachments/assets/c9391716-0ef6-4ca6-878c-954296c2a286)

**index.html**

```html
<form class="purchase-form">
  <label class="purchase-text">구입할 금액을 입력해주세요.</label>
  <div class="purchase-input-wrapper">
    <input
      class="primary-input purchase-input"
      placeholder="금액"
      type="number"
      min="1000"
      max="100000"
    />
    <button class="primary-button purchase-button" type="submit">구입</button>
  </div>
</form>
```

<br>
<br>

### <mark class="yellow">2. 동적인 부분은 js로 구현</mark>

**1\. lottos-container**

구입 금액에 따라 `구입 개수`와 `나의 로또 리스트`를 보여주는 부분이다.

`.lottos-text`에는 `구입 개수`를 **동적**으로 보여주고,  
`.lottos-list`에는 `나의 로또 리스트`를 **동적**으로 보여주어야 한다.

![4](https://github.com/user-attachments/assets/e6679d72-fafb-4f28-adfb-8da461a7a792)

html에는 아래와 같은 코드만 있지만 그림에서는 `ul` 태그 안에 `li` 태그들이 존재한다. 어디서 추가된걸까?

바로 JS에서 동적으로 넣어준 것이다.

**index.html**

```html
<div class="lottos-container" hidden>
  <label class="lottos-text">
    <!-- 구입 개수 js로 구현 -->
  </label>
  <ul class="lottos-list">
    <!-- 나의 로또 리스트 js로 구현 -->
  </ul>
</div>
```

<br>

**src/view/View.js**

```js
const $purchaseButton = elementHandler.$(".purchase-button");
const $lottosContainer = elementHandler.$(".lottos-container");
const $lottosText = elementHandler.$(".lottos-text");
const $lottosList = elementHandler.$(".lottos-list");

const View = {
  renderPurchasedLottos(lottos) {
    $lottosContainer.hidden = false;
    $purchaseButton.disabled = true;
    elementHandler.addClassList($purchaseButton, "disabled");

    $lottosText.textContent = `총 ${lottos.length}개를 구매하였습니다.`;
    $lottosList.innerHTML = `${lottos
      .map(
        (lotto) =>
          `<li class="lottos-list-item"><span>🎟️</span> ${lotto.join(
            ", "
          )}</li>`
      )
      .join("")}`;
  },
};
```

<br>

**2\. winning-lotto-form: 정적+동적**

구입 금액이 유효할 때 뜨는 부분이지만 입력 폼 자체는 정적이기 때문에 html에 미리 구현을 해두었고 hidden 속성만 달아놓았다.

![3](https://github.com/user-attachments/assets/678bccdb-6bd5-41f1-9e90-dbc883f78b77)

**index.html**

```html
<form class="winning-lotto-form" hidden>
  <div class="winning-lotto-text">
    지난 주 당첨번호 6개와 보너스번호 1개를 입력해주세요.
  </div>
  <div class="winning-lotto-container">
    <div class="winning-lotto-wrapper">
      <label>당첨 번호</label>
      <div class="winning-lotto-input-container">
        <!-- 당첨 번호 input js로 구현 -->
      </div>
    </div>
    <div class="winning-lotto-wrapper">
      <label>보너스 번호</label>
      <div class="bonus-lotto-input-container">
        <!-- 보너스 번호 input js로 구현 -->
      </div>
    </div>
  </div>
  <button
    class="primary-button winning-lotto-submit-button"
    type="submit"
    value="결과 확인하기"
  >
    결과 확인하기
  </button>
</form>
```

<br>

**src/view/View.js**

안내 문구나 label은 동적인 부분이지만 그 안의 `input-container`는 입력칸이 반복적으로 렌더링 되는 부분이라 같은 코드를 html에 적는 것보다 js에서 반복하는 것이 효율적이라 생각하여 js에서 구현하였다.

`repeat`을 사용해서 `input` 태그 6개를 한 번에 넣었다.

```js
const $winningLottoForm = elementHandler.$(".winning-lotto-form");
const $winningLottoInputContainer = elementHandler.$(
  ".winning-lotto-input-container"
);
const $bonusLottoInputContainer = elementHandler.$(
  ".bonus-lotto-input-container"
);

const View = {
  renderWinningNumbersInput() {
    $winningLottoForm.hidden = false;

    $winningLottoInputContainer.innerHTML =
      `<input class="primary-input lotto-input winning" type="number" min="1" max="45" required />`.repeat(
        SETTING.LOTTO_LENGTH
      );
    $bonusLottoInputContainer.innerHTML = `<input class="primary-input lotto-input bonus" type="number" min="1" max="45" required />`;
  },
};
```

<br>

**3\. winning-result-table: 정적+동적**

이 부분에서도 정적인 부분과 동적이 부분이 둘 다 있다.

```html
<table class="winning-result-table">
  <thead>
    <tr>
      <th class="winning-result-column head">일치 개수</th>
      <th class="winning-result-column head">당첨금</th>
      <th class="winning-result-column head">당첨 개수</th>
    </tr>
  </thead>
  <tbody class="winning-result-container">
    <!-- 당첨 결과 js로 구현 -->
  </tbody>
</table>
```

<br>

표의 제목인 `thead` 태그 부분은 정적인 부분이기 때문에 html에 구현되어있다.

![5](https://github.com/user-attachments/assets/6730715a-8370-446e-a64a-7452c0bdc96b)

<br>

하지만 당첨 결과 부분은 동적인 부분이기 떄문에 `tbody` 안에 내용이 없다. js에서 동적으로 결과 표를 렌더링했다.

![6](https://github.com/user-attachments/assets/36d12bd7-1e8a-43c7-aa43-cb6989bbceb5)

```js
const $modal = elementHandler.$("#modal");
const $winningResultContainer = elementHandler.$(".winning-result-container");
const $profitRateText = elementHandler.$(".profit-rate-text");

const View = {
  renderWinningResults(winningResults, profitRate) {
    $modal.showModal(); // showModal: <dialog> 내장 함수

    const matchedCounts = [3, 4, 5, "B5", 6];
    $winningResultContainer.innerHTML = matchedCounts
      .map((matchedKey) => {
        const { TITLE_UI, REWARD } = RANKING[matchedKey];
        return `
        <tr>
          <td class="winning-result-column">${TITLE_UI}</td>
          <td class="winning-result-column">${REWARD.toLocaleString()}원</td>
          <td class="winning-result-column">${winningResults[matchedKey]}개</td>
        </tr>`;
      })
      .join("");

    $profitRateText.textContent = `당신의 총 수익률은 ${profitRate}%입니다.`;
  },
};
```

<br>
<br>

## <mark class="pink">🔥2단계 피드백</mark>

### <mark class="yellow">1. 시맨틱 태그 사용하기</mark>

**이전 코드**

```html
<header></header>

<div id="modal"></div>

<div id="main"></div>

<footer></footer>
```

<br>

**피드백**

modal은 `<dialog>`, main 영역은 `<main>` 태그를 쓰면 더 좋을 것 같다.

<br>

**수정한 코드**

```html
<header></header>

<dialog id="modal"></dialog>

<main id="main"></main>

<footer></footer>
```

<br>

**dialog 태그 알아보기**

1\. 주요 특징

- `showModal()`로 열면 모달 외부 요소와의 상호작용이 차단된다.
- ESC 키로 닫기 기능이 기본 제공된다.
- 접근성(WAI-ARIA) 지원이 기본으로 제공된다.(role="dialog", aria-modal="true", 포커스 트랩 등)
- 모바일 환경에서도 잘 작동한다.
- `form method="dialog"`를 사용하면 제출 시 자동으로 모달을 닫을 수 있다.

2\. 주요 메서드

```javascript
// 모달 열기
dialog.showModal(); // 모달 형태로 열기 (배경 딤처리, 외부 클릭 차단)
dialog.show(); // 일반 형태로 열기 (모달이 아닌 일반 요소처럼 표시)

// 모달 닫기
dialog.close(); // 모달 닫기
```

3\. 주요 속성

```javascript
// 모달이 열려있는지 확인
dialog.open; // true/false 반환

// 반환값 설정
dialog.returnValue; // form method="dialog"가 제출될 때의 반환값
```

4\. 주요 이벤트

```javascript
// 이벤트 리스너 예시
dialog.addEventListener("close", () => {
  // 모달이 닫힐 때 실행
});

dialog.addEventListener("cancel", () => {
  // ESC 키로 모달이 닫힐 때 실행
});
```

5\. CSS 가상 클래스

```css
/* 모달이 열려있을 때의 스타일 */
dialog::backdrop {
  background: rgba(0, 0, 0, 0.5); /* 배경 딤처리 */
}

/* 모달이 열려있지 않을 때 */
dialog:not([open]) {
  display: none;
}
```

6\. 사용 예시

```html
<dialog id="modal">
  <form class="primary-container modal__content" method="dialog">
    <button class="close-button">❌</button>
    <h2 class="modal__title">🏆 당첨 통계 🏆</h2>
    <!-- 당첨 결과 생략 -->
    <button class="primary-button restart-button">다시 시작하기</button>
  </form>
</dialog>
```

```js
const View = {
  renderWinningResults(winningResults, profitRate) {
    $modal.showModal();

    this.renderCloseModal();
    this.renderRestartGame();
  },

  renderCloseModal() {
    eventHandler.onClick($modal, () => $modal.close());
    eventHandler.onClick($modalContent, (event) => {
      event.stopPropagation();
    });
  },

  renderRestartGame() {
    eventHandler.onClick($restartButton, () => {
      $modal.close();
    });
  },
};
```

<br>
<br>

### <mark class="yellow">2. innerHTML vs insertAdjacentHTML vs createElement</mark>

**insertAdjacentHTML**

1\. 성능상의 이점: innerHTML은 전체 DOM을 다시 파싱하고 렌더링하지만, insertAdjacentHTML은 지정된 위치에만 새로운 요소를 삽입하므로 더 효율적이다.(채팅 등)

2\. 기존 이벤트 리스너 보존: innerHTML은 기존 요소를 모두 제거하고 새로 만들기 때문에 이벤트 리스너가 사라진다. insertAdjacentHTML은 기존 요소와 이벤트 리스너를 유지한다.

3\. XSS 공격에 취약: innerHTML과 마찬가지로 XSS 공격에 취약하다.

4\. 누적이 아니라면 초기화 해야함: 초기화하지 않으면 새로운 데이터가 기존 데이터에 계속 추가된다.

```
beforebegin
<element>
  afterbegin
  내용
  beforeend
</element>
afterend
```

```js
const element = document.querySelector("#example");

// 1. 'beforebegin': 요소 바로 앞에
element.insertAdjacentHTML("beforebegin", "<div>이전에 추가</div>");

// 2. 'afterbegin': 요소의 첫 자식으로
element.insertAdjacentHTML("afterbegin", "<div>첫 자식으로 추가</div>");

// 3. 'beforeend': 요소의 마지막 자식으로
element.insertAdjacentHTML("beforeend", "<div>마지막 자식으로 추가</div>");

// 4. 'afterend': 요소 바로 뒤에
element.insertAdjacentHTML("afterend", "<div>다음에 추가</div>");
```

<br>

이전 코드

```js
$lottosList.innerHTML = `${lottos
  .map(
    (lotto) =>
      `<li class="lottos-list-item"><span>🎟️</span> ${lotto.join(", ")}</li>`
  )
  .join("")}`;
```

<br>

개선된 코드

```js
$lottosList.textContent = ""; // 기존 내용 초기화
lottos.forEach((lotto) => {
  $lottosList.insertAdjacentHTML(
    "beforeend",
    `<li class="lottos-list-item"><span>🎟️</span> ${lotto.join(", ")}</li>`
  );
});
```

<br>

**createElement**

1\. 장점

- 더 안전한 XSS 방어
- 더 나은 타입 안정성
- 더 명확한 코드 구조
- 복잡한 DOM 조작에서 더 나은 성능

2\. 단점

- 더 많은 코드 작성 필요
- 간단한 HTML 구조에서는 덜 직관적
- 작은 규모의 단순 변경에서는 innerHTML이 더 빠를 수 있음

<br>

개선된 코드

```js
const fragment = document.createDocumentFragment();

lottos.forEach((lotto) => {
  const li = document.createElement("li");
  li.className = "lottos-list-item";

  const span = document.createElement("span");
  span.textContent = "🎟️";

  li.appendChild(span);
  li.appendChild(document.createTextNode(` ${lotto.join(", ")}`));
  fragment.appendChild(li);
});

$lottosList.textContent = ""; // 기존 내용 초기화
$lottosList.appendChild(fragment);
```

<br>
<br>

### <mark class="yellow">3. 구입 버튼 두 번 클릭 시 에러 발생</mark>

**문제점**

금액을 입력한 후 구입 버튼을 두 번 누르면 어떤 숫자를 입력해도 에러가 발생한다.

![7](https://github.com/user-attachments/assets/8fe5a73d-3f3c-48f0-b40b-1ab94be2dd18)

<br>

**이유**

**src/controller/LottoGameController2.js**

이벤트 리스너가 중복 등록되기 때문이다.  
첫 번째 리스너는 입력값이 DOM에 반영되기 전에 실행되어 빈 값('')을 읽게 되고,  
그 다음 두 번째 리스너가 실제 입력된 값을 읽어서 처리한다.

```js
#inputWinningLotto() {
  const $winningInputs = elementHandler.$$('.lotto-input.winning');
  const $bonusInput = elementHandler.$('.lotto-input.bonus');
  $winningInputs[0].focus();

  // 이 이벤트 리스너가 구매 버튼을 누를 때마다 새로 등록됨
  $winningLottoForm.addEventListener('submit', (event) => {
    event.preventDefault();
    const winningNumberList = [...$winningInputs].map((winningNumber) => Number(winningNumber.value));
    const bonusNumber = Number($bonusInput.value);
    // ...
  });
}
```

<br>

**수정한 코드**

**src/view/View.js**

구입 버튼을 눌러 `로또 리스트`가 화면에 렌더링 되면 `$purchaseButton`에 `disabled` 속성을 true로 바꾸어서 중복 실행을 방지한다.

```js
renderPurchasedLottos(lottos) {
  $lottosContainer.hidden = false;
  $purchaseButton.disabled = true;
  elementHandler.addClassList($purchaseButton, 'disabled');

  $lottosText.textContent = `총 ${lottos.length}개를 구매하였습니다.`;
  $lottosList.innerHTML = `${lottos
    .map((lotto) => `<li class="lottos-list-item"><span>🎟️</span> ${lotto.join(', ')}</li>`)
    .join('')}`;
},
```

![8](https://github.com/user-attachments/assets/95653a28-7928-4dff-bf57-6aea26f98d63)

<br>
<br>

### <mark class="yellow">4. 유효성 검사 통과 못 했을 때 모달창 뜸</mark>

**문제점 & 이유**

**src/controller/LottoGameController2.js**

`#checkBonusNumber`가 try 됐을 때 `this.#showResult();`를 실행해서 당첨 번호의 유효성 검사가 통과하지 못 해도 보너스 번호의 유효성 검사가 통과한다면 모달창이 뜨는 것을 확인 했다.

```js
#inputWinningLotto() {
  // 생략

  $winningLottoForm.addEventListener('submit', (event) => {
    event.preventDefault();
    const winningNumberList = [...$winningInputs].map((winningNumber) => Number(winningNumber.value));
    const bonusNumber = Number($bonusInput.value);
    this.#checkWinningNumbers(winningNumberList);
    this.#checkBonusNumber(bonusNumber, winningNumberList);
  });
}

#checkWinningNumbers(winningNumberList) {
  const $winningInputs = elementHandler.$$('.lotto-input.winning');

  try {
    Validator.validateWinningNumberList(winningNumberList);
    this.#winningNumbers = winningNumberList;
  } catch (error) {
    alert(error.message);
    $winningInputs[0].focus();
  }
}

#checkBonusNumber(bonusNumber, winningNumberList) {
  try {
    Validator.validateBonusNumber(bonusNumber, winningNumberList);
    this.#bonusNumber = bonusNumber;
    this.#showResult(); // 보너스 번호 유효성 검사만 통과해도 모달이 뜸
  } catch (error) {
    alert(error.message);
  }
}
```

<br>

**수정한 코드**

**src/controller/LottoGameController2.js**

`this.#showResult();` 실행을 `#inputWinningLotto()` 메소드의 역할로 옮긴 후  
이 메소드 안에서 두 검증을 모두 통과했는지 확인하고 통과했다면 모달창을 띄우는 것으로 변경했다.

```js
#inputWinningLotto() {
    const $winningInputs = elementHandler.$$('.lotto-input.winning');
    const $bonusInput = elementHandler.$('.lotto-input.bonus');
    $winningInputs[0].focus();

    $winningLottoForm.addEventListener('submit', (event) => {
      event.preventDefault();
      const winningNumberList = [...$winningInputs].map((winningNumber) => Number(winningNumber.value));
      const bonusNumber = Number($bonusInput.value);

      // 두 검증을 모두 통과해야만 결과를 보여줌
      if (this.#validateWinningNumbers(winningNumberList) &&
          this.#validateBonusNumber(bonusNumber, winningNumberList)) {
        this.#winningNumbers = winningNumberList;
        this.#bonusNumber = bonusNumber;
        this.#showResult();
      }
    });
}

// 검증 메서드들을 boolean을 반환하도록 수정
#validateWinningNumbers(winningNumberList) {
    const $winningInputs = elementHandler.$$('.lotto-input.winning');
    try {
      Validator.validateWinningNumberList(winningNumberList);
      return true;
    } catch (error) {
      alert(error.message);
      $winningInputs[0].focus();
      return false;
    }
}

#validateBonusNumber(bonusNumber, winningNumberList) {
    try {
      Validator.validateBonusNumber(bonusNumber, winningNumberList);
      return true;
    } catch (error) {
      alert(error.message);
      return false;
    }
}
```

<br>
<br>

## <mark class="pink">🔥개념과 함께 채우기</mark>

### <mark class="yellow">DOM 변경을 비싼 작업이라고 하는 이유는 무엇일까?</mark>

**DOM을 추가/삭제/수정하면 브라우저에서는 어떤 일이 일어날까?**

<br>
<br>

### <mark class="yellow">이벤트 핸들러 함수를 만들 때 bind(this) vs 화살표 함수의 차이</mark>

**this - 함수가 실행될 때 this가 어떤 걸로 결정되는 지 예상할 수 있는지?**

**일반 함수와 화살표 함수는 무슨 차이가 있을까?**

- this binding, call/apply/bind
