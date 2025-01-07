---
layout: single
title: "[우테코]Level1 로또 게임 1단계"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션2 🎱로또 게임 1단계

## <mark class="pink">🔥1단계 구현 사항</mark>

[1단계 - 콘솔 기반 로또 게임](https://github.com/woowacourse/javascript-lotto/pull/281){: .btn .btn--primary}

### <mark class="yellow">주요 코드</mark>

**Lotto.js**

각 로또의 번호(접근 제어자)와 countMatchedNumbers와 hasNumber(보너스 넘버 유무)를 반환하는 메소드를 가진 클래스인다.  
countMatchedNumbers 메소드에서는 hasNumber라는 메소드를 활용한다.

```js
class Lotto {
  #lotto;

  constructor(lotto) {
    this.#lotto = lotto;
  }

  countMatchedNumbers(winningNumbers) {
    const count = winningNumbers.filter((number) =>
      this.hasNumber(number)
    ).length;
    return count;
  }

  hasNumber(number) {
    return this.#lotto.includes(number);
  }
}

export default Lotto;
```

<br>

**LottoMachine.js**

구매 금액으로 클래스를 생성하고 몇 장의 로또를 샀는지 정한다. 그 개수만큼 로또 번호를 생성한다.  
로또 번호는 중복되면 안 되므로 집합으로 구현한 후 반복해서 랜덤한 값을 넣어주었다. 완성된 로또 번호는 문제에 나와있는 대로 오름차순으로 정렬하여 return 했다.

```js
import pickRandomNumberInRange from "../util/pickRandomNumberInRange.js";
import { SETTING } from "../constant/setting.js";

class LottoMachine {
  #purchaseAmount;

  constructor(purchaseAmount) {
    this.#purchaseAmount = purchaseAmount;
  }

  getLottoNumberList() {
    return Array.from({
      length: this.#purchaseAmount / SETTING.LOTTO_PRICE,
    }).map((_) => this.#createLottoNumber());
  }

  #createLottoNumber() {
    const lotto = new Set();
    while (lotto.size < SETTING.LOTTO_LENGTH) {
      lotto.add(
        pickRandomNumberInRange(
          SETTING.MIN_LOTTO_NUMBER,
          SETTING.MAX_LOTTO_NUMBER
        )
      );
    }
    return [...lotto].sort(
      (firstNumber, secondNumber) => firstNumber - secondNumber
    );
  }
}

export default LottoMachine;
```

<br>

**LottosManager.js**

당첨 결과를 계산하는 클래스이다.

`lottoList`는 `LottoMachine`을 통해 만들어진 내가 산 로또 리스트이다. 이 로또는 현재 이차원 배열로 되어있는 상태고 각 로또로 `Lotto` 클래스를 만들어 `[Lotto, Lotto, ---]` 클래스 배열로 변경했다.

`getWinningResults` 안에 `winningResults` 객체가 있는데 그 객체는 `updateWinningResults`에서도 사용되므로 클래스 필드로 옮기는 것도 좋은 방법인 것 같다.

```js
import Lotto from "./Lotto.js";
import { SETTING, RANKING } from "../constant/setting.js";

class LottosManager {
  #lottos;

  constructor(lottoList) {
    this.#lottos = lottoList.map((lotto) => new Lotto(lotto));
  }

  getWinningResults(winningNumbers, bonusNumber) {
    const winningResults = {
      3: 0,
      4: 0,
      5: 0,
      B5: 0,
      6: 0,
    };

    this.#lottos.forEach((lotto) => {
      const matchedNumbers = lotto.countMatchedNumbers(winningNumbers);
      const isBonusMatched = lotto.hasNumber(bonusNumber);
      this.#updateWinningResults(
        winningResults,
        matchedNumbers,
        isBonusMatched
      );
    });
    return winningResults;
  }

  #updateWinningResults(winningResults, matchedNumbers, isBonusMatched) {
    if (matchedNumbers >= SETTING.MIN_RANKING_MATCHING_NUMBER) {
      const matchedKey =
        matchedNumbers === RANKING.B5.MATCHING_COUNT && isBonusMatched
          ? "B5"
          : matchedNumbers;
      winningResults[matchedKey] += 1;
    }
  }
}

export default LottosManager;
```

<br>
<br>

### <mark class="yellow">1. InputController 생성</mark>

**구현 사항**

InputController에서 입력에 관한 코드를 하나로 분리하였다.

<br>

**이유**

1차 미션때는 이 부분이 없었다. tryCatch를 util 함수로 관리했기 때문이다.  
이번 미션 때도 똑같이 관리하려고 했지만 보너스 함수를 유효성 검사하는 과정에서 당첨번호를 인자로 같이 넘겨줘야 했고, 이 부분에서 오류가 떴었다.  
메인 컨트롤러에서 재입력을 받는 코드를 처음에 짰지만 4개나 입력을 받아야 해서 따로 InputController 파일을 만들었다.

<br>
<br>

## <mark class="pink">🔥1단계 피드백</mark>

### <mark class="yellow">1. 조건 충족할 때만 메소드 호출하기</mark>

**이전 코드**

**src/domain/Lottos.js**

모든 상황에서 `getRanking`을 호출하고 일치한 숫자 개수가 3개 미만이면 `''`를 반환한다.

```js
getWinningResults(winningNumbers, bonusNumber) {
  const winningResults = this.#initWinningResults();
  this.#lottos.forEach((lotto) => {
    const ranking = this.#getRanking(lotto, winningNumbers, bonusNumber);
    if (winningResults.hasOwnProperty(ranking)) {
      winningResults[ranking] += 1;
    }
  });
  return winningResults;
}

#getRanking(lotto, winningNumbers, bonusNumber) {
  const matchedNumbers = lotto.countMatchedNumbers(winningNumbers);
  if (matchedNumbers === RANKING.SECOND.MATCHING_COUNT && lotto.hasNumber(bonusNumber)) {
    return RANKING.SECOND.NAME;
  }
  return this.#winningCriteria[matchedNumbers >= SETTING.MIN_RANKING_MATCHING_NUMBER ? matchedNumbers : ''];
}
```

<br>

**피드백**

matchedNumbers가 3 이상일 때만 winningCriteria에서 값을 가져와서 반환하고, 아니면 undefined(빈 문자열)를 반환하는 부분이 있다.

리뷰어는 후자를 선호하는 편이었다. 지금의 방법처럼 조건에 맞지 않는 경우 고의로 객체에 빈 문자열을 넣어서 undefined 값을 유도하는 것은 가독성과 휴먼에러 두 마리 토끼를 모두 방생하는 방법이라는 피드백을 주셨다.

<br>

**수정한 코드**

**src/domain/LottosManager.js**

Lottos와 이름만 바뀐 같은 파일이다.

if문을 추가하여 3(MIN_RANKING_MATCHING_NUMBER) 이상일 때만 winningResults를 갱신하는 코드가 실행되게 하였다.

```js
#updateWinningResults(winningResults, matchedNumbers, isBonusMatched) {
  if (matchedNumbers >= SETTING.MIN_RANKING_MATCHING_NUMBER) {
    const matchedKey = matchedNumbers === RANKING.B5.MATCHING_COUNT && isBonusMatched ? 'B5' : matchedNumbers;
    winningResults[matchedKey] += 1;
  }
}
```

<br>
<br>

### <mark class="yellow">2. 예외 케이스 실험하기</mark>

**예외 케이스**

1\. 앞 뒤에 콤마가 하나 더 들어가면 숫자가 7개로 인식된다.

2\. 매우 큰 금액(1000000000000)을 입력하면 에러가 나면서 터진다.
![image](https://github.com/user-attachments/assets/73335faa-3a0b-4c61-ba43-03b017e47726)

<br>

**피드백**

처음부터 모든 상황을 고려할 순 없지만 에러가 터지고 나서 뒷처리를 어떻게 하느냐가 중요하다. 에러를 수정한 후 테스트 코드 작성, 해당 에러에 대한 문서화까지 습관화해두면 도움이 될 거다.

<br>

**수정한 코드**

**src/validator/Validator.js**

JavaScript의 number 타입이 표현할 수 있는 최대값은 1.7976931348623157e+308이지만, 금액 관련 계산이나 특정 반복 로직이 메모리 문제를 유발했다.

`LottoMachine` 클래스에서 구입 금액으로 로또 리스트 배열을 만들고 각 리스트 배열에서는 최소 6번 랜덤 숫자를 생성했기 때문에 이 부분에서 힙 메모리를 추가하였다.

따라서 구입 금액에 한도를 걸어서 오류를 해결하였다.

```js
purchaseAmountRange(input) {
  if (input > 100000000) {
    throw new Error(ERROR_MESSAGE.PURCHASE_AMOUNT_RANGE);
  }
},
```

**\_\_tests\_\_/Validator.test.js**

```js
describe("[Validator] 로또 구입 금액 검증", () => {
  test.each`
    title                                        | input          | errorMessage
    // 나머지 테스트 코드
    ${"입력 값은 최대 100000000 이하여야 한다."} | ${"100001000"} | ${ERROR_MESSAGE.PURCHASE_AMOUNT_RANGE}
  `("$title", ({ input, errorMessage }) => {
    const validation = () => Validator.validatePurchaseAmount(input);
    expect(validation).toThrow(errorMessage);
  });
});
```

<br>

**src/controller/InputController.js**

```js
// 이전 코드
return winningNumbers.split(",").map((number) => parseInt(number.trim()));

// 수정한 코드 (빈 문자 제거)
// " , 123, , 456"일 때 ['123', '456']로 변환
return winningNumbers
  .split(",")
  .filter((item) => item.trim() !== "")
  .map(Number);
```

<div class="blue-box">
  <p>
    <b>💡parseInt() vs Number</b>
    <div>parseInt()는 숫자만 추출하고 나머지 문자는 무시한다. "123abc"는 "123"으로 변환된다.</div>
    <div>Number는 문자열 전체가 유효한 숫자여야만 제대로 숫자로 변환된다. "123abc"는 NaN으로 변환된다.</div>
  </p>
</div>

<br>
<br>

### <mark class="yellow">3. 식 vs 문</mark>

**틀린 코드**

```js
#calculateProfitRate(winningResults) {
  const totalProfit = Object.entries(winningResults).reduce((profit, [ranking, count]) => {
    return profit += RANKING[ranking].REWARD * count;
  })
}
```

<br>

**식 vs 문**

1\. 식

- 값을 생성하는 코드
- 반드시 값을 반환하며 이 값은 다른 식의 일부로 사용될 수 있다.

```js
const x = 1 + 2; // '1 + 2'는 식이며, 결과값 3을 반환
console.log(x * 2); // 'x * 2'도 식이며, 결과값 6을 반환
const y = x > 3 ? "크다" : "작다"; // 삼항 연산자는 식이며, '작다' 반환
```

2\. 문

- 특정 작업을 수행하는 코드
- 값을 반환하지 않고 독립적으로 실행된다.

```js
if (x > 0) {
  // 조건문은 문
  console.log("양수입니다."); // 실행만 할 뿐 값을 반환하지 않음
}

let z = 0; // 변수 선언문도 문 (값을 반환하지 않음)
```

<br>

**수정한 코드**

```js
// 식
return profit + RANKING[ranking].REWARD * count;

// 문+식
profit += RANKING[ranking].REWARD * count; // 문: 변수 상태 변경 작업 수행 후 반환하지 않음
return profit;

// 식+식
const result = profit + RANKING[ranking].REWARD * count;
return result;
```

<br>
<br>

### <mark class="yellow">4. hasOwnProperty vs in 연산자</mark>

**예시 코드**

`parentObj`의 프로퍼티에는 `inheritedKey`가 있고,  
`obj`에 `ownKey`라는 프로퍼티를 추가한다.

```js
const parentObj = { inheritedKey: 42 };
const obj = Object.create(parentObj); // obj의 프로토타입에 parentObj 추가
obj.ownKey = "Hello";
```

<br>

**hasOwnProperty**

- 객체의 직접 소유한 프로퍼티만 확인(프로토타입 체인 무시)
- 프로토타입 체인을 포함하지 않고 객체 자신이 정의한 프로퍼티에 대해서만 `true`를 반환

```js
console.log(obj.hasOwnProperty("ownKey")); // true
console.log(obj.hasOwnProperty("inheritedKey")); // false(parentObj에 있는 프로퍼티는 false)
```

<br>

**in 연산자**

- 객체와 객체의 프로토타입 체인에서 해당 프로퍼티가 존재하는지를 확인
- 프로토타입 체인을 포함하여 해당 키가 있으면 `true`를 반환

```js
console.log("ownKey" in obj); // true
console.log("inheritedKey" in obj); // true (프로토타입 체인에 존재)
```

<br>

**개선 방안**

객체의 소유 프로퍼티만 확인하는 시도는 좋다. 하지만 `hasOwnProperty`는 예전에 쓰던 문법이고 현재는 권장되지 않는다.

ES2022를 사용할 수 있다면 Object.hasOwn()를 사용하는 것이 더 나은 선택이다.
