---
layout: single
title: "프리코스 4주차"
categories: 우아한테크코스
toc: true
toc_sticky: true
---

# 🎄 크리스마스 이벤트

<mark style='color: red'>※ 진블로그는 링크가 있는 사람만 조회할 수 있어서 저만의 기록 공간으로 활용하고 있습니다.</mark>

## <mark style='background-color: #ffdce0'>2023.11.10(금)</mark>

### 🧐 3주차 공통 피드백

- 함수가 15라인이 넘어가면 고민하자.
- 예외 사항을 다 고려하자.
  (로또 번호 입력의 예외 처리 예시는 모두 하였다.)
- 비즈니스 로직과 UI 로직을 분리하자.
  (해당 클래스가 비즈니스 로직이라면 메서드에 print()가 없어야 한다.)
- 객체의 상태를 외부에서 직접 접근하는 방식을 최소화 하자.
- getter와 setter의 사용을 지양하자.
  (상태 데이터를 꺼내 로직을 처리하도록 구현하지 말고 객체에 메시지를 보내 일을 하도록 리팩토링한다.)
- 필드 수를 줄이기 위해 노력하자.
- 테스트 코드도 리펙토링 하자.
  (반복하는 부분은 중복으로 만들지 않는다. test.each([[], [], []])(”메세지”, 함수) 형태로 쓰면 된다.)
- 테스트가 우선이 되지 말자.
  (내 코드에서 테스트 코드를 작성해야 한다. 테스트 코드에 내 코드를 맞추지 말자.)
- 단위 테스트가 어려운 부분은 분리하고 쉬운 부분만 테스트하자.
  (랜덤 로또 출력은 테스트가 어려우므로 분리한 후 다른 클래스만 테스트 한다.)

### 🚀 기능 요구사항

<mark style='background-color: #fff5b1'>‘12월 이벤트 플래너’를 만들어 보자.</mark>

**1\. 시작 입력**

```
안녕하세요! 우테코 식당 12월 이벤트 플래너입니다.
```

---

**2\. 방문 날짜 입력**

```
12월 중 식당 예상 방문 날짜는 언제인가요? (숫자만 입력해 주세요!)
3
```

※ 예외처리

- 공백인 경우
- 숫자가 아닌 경우
- 1~31 사이가 아닌 경우

`"[ERROR] 유효하지 않은 날짜입니다. 다시 입력해 주세요.”`

---

**3\. 주문 메뉴와 개수 입력**

```
주문하실 메뉴를 메뉴와 개수를 알려 주세요. (e.g. 해산물파스타-2,레드와인-1,초코케이크-1)
티본스테이크-1,바비큐립-1,초코케이크-2,제로콜라-1
```

※ 예외처리1

`"[ERROR] 유효하지 않은 주문입니다. 다시 입력해 주세요."`

- 메뉴판에 없는 메뉴를 입력하는 경우
- 개수가 1 이상의 숫자가 아닌 경우

※ 예외처리2

`"[ERROR] 유효하지 않은 주문입니다. 다시 입력해 주세요."`

- 공백인 경우
- 메뉴 형식이 예시와 다른 경우
- 쉼표 위치, 띄어쓰기, 하이픈이 잘못 입력된 경우

※ 예외처리3

`"[ERROR] 유효하지 않은 주문입니다. 다시 입력해 주세요."`

- 중복 메뉴를 입력한 경우

※ 예외처리4

- 총주문 금액 10,000원 이상부터 이벤트가 적용됩니다.(이건 어디서 안내하지?)

※ 예외처리5

`"[ERROR] 음료만 주문할 수 없습니다. 다시 입력해 주세요."`

- 음료만 주문했을 경우

※ 예외처리6

`"[ERROR] 메뉴는 20개까지만 주문할 수 있습니다. 다시 입력해 주세요."`

- 메뉴를 20개 넘게 주문했을 경우

---

**4\. 이벤트 혜택 결과 출력하기**

**4-1. 문구 출력**

입력한 날짜를 출력하면 된다.

```
12월 3일에 우테코 식당에서 받을 이벤트 혜택 미리 보기!
```

**4-2. 주문 메뉴 출력**

출력 순서는 자유롭게

```
<주문 메뉴>
티본스테이크 1개
바비큐립 1개
초코케이크 2개
제로콜라 1개
```

**4-3. 총주문 금액 출력**

```
<할인 전 총주문 금액>
142,000원
```

**4-4. 증정 메뉴 출력**

없는 경우 `없음` 출력

```
<증정 메뉴>
샴페인 1개
```

**4-5. 혜택 내역 출력**

- 고객에게 적용된 이벤트 내역만 보여주기
- 없는 경우 `없음` 출력
- 출력 순서는 자유롭게

1\. 크리스마스 디데이 할인

- 12.1~12.25
- 1000원으로 시작하여 매일 100원씩 증가
- 12월 1일: 1000원 ~ 12월 25일: 3400원 할인

2\. 평일 할인(일~목): 디저트 메뉴 1개당 2023원 할인

3\. 주말 할인(금,토) : 메인 메뉴 1개당 2023원 할인

4\. 특별 할인(3, 10, 17, 24, 25, 31): 1000원 할인

5\. 증정 이벤트: 할인 전 금액이 12만원 이상일 때, 샴페인 1개 증정(25000원)

6\. 1번 외 나머지는 12.1~12.31

```
<혜택 내역>
크리스마스 디데이 할인: -1,200원
평일 할인: -4,046원
특별 할인: -1,000원
증정 이벤트: -25,000원
```

**4-6. 총 혜택 금액 출력**

할인 금액의 합계 + 증정 메뉴의 가격

```
<총혜택 금액>
-31,246원
```

**4-7. 할인 후 금액 출력**

할인 전 총 주문 금액 - 할인 금액

```
<할인 후 예상 결제 금액>
135,754원
```

**4-8. 배지 출력**

없는 경우 `없음` 출력

총 혜택 금액이  
5천원 이상: 별  
1만원 이상: 트리  
2만원 이상: 산타

```
<12월 이벤트 배지>
산타
```

---

### 🎯 프로그래밍 요구사항

※ 추가된 요구사항

- `InputView`, `OutputView` 객체를 활용해 구현한다.
- 입력과 출력을 담당하는 객체를 별도로 구현한다.
- 파일 경로는 변경할 수 있다.
- 메서드의 이름과 인자는 필요에 따라 추가하거나 변경할 수 있다.

---

### 📝 공부한 것

**MVC 패턴**

이번엔 `InputView`, `OutputView` 파일이 이미 있는 걸 보니 예상했던 대로 정말 MVC 패턴으로 구현해야 하는 것 같다. 저번주에 유효성 검사와 재입력 때문에 실패했는데 이번엔 꼭 성공할거다.

**[10분 테코톡] 🧀 제리의 MVC 패턴**

👉🏻 [https://www.youtube.com/watch?v=ogaXW6KPc8I](https://www.youtube.com/watch?v=ogaXW6KPc8I)

1\. MVC는 왜 생겨난걸까?

- 유지보수가 편해지는 코드 구성 방식

2\. MVC 맛보기

- 사용사 —’코딩’이라고 검색—>Controller
- Controller —검색 결과 데이터 달라고 요청—> Model
- Controller —받은 검색 결과 데이터를 전달—> View
- View —검색 결과—> 사용자

3\. MVC

- Model: 데이터와 관련된 부분
- View: 사용자한테 보여지는 부분
- Controller: Model과 View를 이어주는 부분

4\. MVC를 지키면서 코딩하는 방법

- Model 내부에 컨트롤러와 뷰에 관련된 코드가 있으면 안 된다.
- View 내부에는 Model의 코드만 있을 수 있고, Controller의 코드는 있으면 안 된다.
- 사용자마다 다르게 보여주어야 하는 데이터에 대해서만 받아야 한다.
- Controller 내부에는 Model과 View에 대한 코드가 있어도 된다.
- View가 Model로부터 데이터를 받을 때, 반드시 Controller에서 받아야 한다.

## <mark style='background-color: #ffdce0'>2023.11.11(토)</mark>

### 🎯 프로젝트 기능 명세서

```markdown
# 프로젝트 소개

2023 우아한테크코스 6기의 4주차 프리코스 미션 과제입니다. 이번 과제는 12월 이벤트를 구현하는 것입니다.

# 주요 기능

- 할인 이벤트: 크리스마스 디데이 할인, 평일 할인, 주말 할인, 특별 할인이 있습니다. 이 할인은 중복될 수 있습니다. 클리스마스 디데이 할인을 제외하고 12월 한달 간 진행됩니다.

- 증정 이벤트: 12만원 이상 주문 시 25000원 샴페인을 증정해드립니다. 위 할인과 중복 사용 가능합니다.

- 배지: 총 혜택 금액에 따라 다른 이벤트 배지를 받을 수 있습니다. 이 배지는 2024 새해 이벤트에서 활용될 예정입니다.

- 서비스 기능: 방문 날짜와 메뉴를 미리 선택하면 받을 수 있는 이벤트들을 보여드립니다.

# 🎯 프로젝트 기능 명세서

**1\. 메뉴 상수화**

- [ ] 애피타이저, 메인, 디저트, 음료 별로 메뉴와 가격을 저장해둔다.

**2\. 이벤트 할인 함수**

- [ ] 크리스마스 디데이 할인 함수를 만든다.
- [ ] 평일 할인이 되는 날짜를 저장하고 디저트 메뉴를 개당 2023원 할인하는 함수를 만든다.
- [ ] 주말 할인이 되는 날짜를 저장하고 메인 메뉴를 개당 2023원 할인하는 함수를 만든다.
- [ ] 특별 할인 되는 날짜를 저장하고 할인하는 함수를 만든다.
- [ ] 25000원 샴페인 증정 이벤트 함수를 만든다.

**3\. 배지 함수**

- [ ] 총 혜택 금액에 따른 배지를 결정한다.

**4\. 방문 날짜 입력**

- [ ] 방문 날짜를 숫자로 입력 받는다.
- [ ] 옳은 입력을 할 때까지 입력 받는다.

※ 예외 처리
`"[ERROR] 유효하지 않은 날짜입니다. 다시 입력해 주세요.”`

- [ ] 공백일 경우 예외 처리한다.
- [ ] 숫자가 아닐 경우 예외 처리한다.
- [ ] 1부터 31 사이의 숫자가 아닐 경우 예외 처리한다.

**5\. 주문 메뉴와 개수 입력**

- [ ] `메뉴-개수` 형식으로 입력 받는다.
- [ ] 띄어쓰기나 쉼표의 오타는 재입력이 아니라 바르게 처리한다.
- [ ] 하이픈의 오타는 재입력을 받는다.

※ 예외 처리1
`"[ERROR] 유효하지 않은 주문입니다. 다시 입력해 주세요.”`

- [ ] 공백인 경우 예외 처리한다.
- [ ] 메뉴 형식이 예시와 다른 경우 예외처리한다.
- [ ] 하이픈이 잘못 입력된 경우 예외 처리한다.
- [ ] 메뉴판에 없는 메뉴를 입력하는 경우 예외 처리한다.
- [ ] 개수가 1 이상의 숫자가 아닌 경우 예외 처리한다.
- [ ] 중복 메뉴를 입력한 경우 예외 처리한다.

※ 예외 처리2
`"[ERROR] 음료만 주문할 수 없습니다. 다시 입력해 주세요.”`

- [ ] 음료만 주문했을 경우 예외 처리한다.

※ 예외 처리3
`"[ERROR] 메뉴는 20개까지만 주문할 수 있습니다. 다시 입력해 주세요.”`

- [ ] 메뉴를 20개 넘게 주문했을 경우 예외 처리한다.

**6\. 이벤트 혜택 결과 출력**

- [ ] 입력한 날짜로 처음 안내 문구 출력한다.
- [ ] 주문 메뉴와 개수를 순서 상관없이 출력한다.
- [ ] 할인 전 총 주문 금액을 출력한다.
- [ ] 증정 메뉴를 출력한다. 없으면 `없음` 출력한다.
- [ ] 고객에게 적용된 이벤트 내역만 순서 상관없이 출력하고 없는 경우 `없음` 을 출력한다.
- [ ] 할인 금액과 증정 메뉴를 합한 총 혜택 금액을 출력한다.
- [ ] 총 주문 금액에서 할인 금액을 뺀 할인 후 금액을 출력한다.
- [ ] 배지를 출력하고 없는 경우 `없음`을 출력한다.
```

---

### 🎯 폴더 구조

```
src/
├── constants/
│   ├── menuInfo.js
│   ├── event.js
│   ├── gameMessage.js
│   └── errorMessage.js
├── controllers/
│   ├── InputController.js
│   └── MainController.js
├── models/
│   ├── CalculatePrice.js
│   ├── EventBadge.js
│   ├── EventDiscount.js
│   └── EventGift.js
├── views/
│   ├── InputView.js
│   └── OutputView.js
├── utils/
│   ├── CreateOrderList.js
│   ├── PriceFormat.js
│   └── Validate.js
├── App.js
└── index.js
```

---

### 🎯 1. 메뉴 상수화

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>애피타이저, 메인, 디저트, 음료 별로 메뉴와 가격을 저장해둔다.</div>
  </p>
</div>

```jsx
const menuInfo = Object.freeze({
  양송이수프: { price: 6000, category: "appetizer" },
  타파스: { price: 5500, category: "appetizer" },
  시저샐러드: { price: 8000, category: "appetizer" },
  티본스테이크: { price: 55000, category: "main" },
  바비큐립: { price: 54000, category: "main" },
  해산물파스타: { price: 35000, category: "main" },
  크리스마스파스타: { price: 25000, category: "main" },
  초코케이크: { price: 15000, category: "dessert" },
  아이스크림: { price: 5000, category: "dessert" },
  제로콜라: { price: 3000, category: "drink" },
  레드와인: { price: 60000, category: "drink" },
  샴페인: { price: 25000, category: "drink" },
});
export default menuInfo;
```

---

### 🎯 2. 할인이벤트 상수화

```jsx
const event = Object.freeze({
  DAY: {
    FIRST: 1,
    LAST: 31,
    CHRISTMAS: 25,
  },
  DISCOUNT: {
    CONDITION: 10000,
    DDAY_INITIAL: 1000,
    DDAY_ADD: 100,
    WEEK: 2023,
    SPECIAL: 1000,
  },
  GIFT: {
    CONDITION: 120000,
    EVENT: 25000,
  },
  BADGE: {
    STAR: 5000,
    TREE: 10000,
    SANTA: 20000,
  },
});
export default event;
```

---

### 🎯 3. gameMessage.js

```jsx
const gameMessage = Object.freeze({
  INPUT: {
    DATE: "12월 중 식당 예상 방문 날짜는 언제인가요? (숫자만 입력해 주세요!)\n",
    ORDER:
      "주문하실 메뉴를 메뉴와 개수를 알려 주세요. (e.g. 해산물파스타-2,레드와인-1,초코케이크-1)\n",
    BONUS: "\n보너스 번호를 입력해 주세요.\n",
  },

  OUTPUT: {
    GREETING: "안녕하세요! 우테코 식당 12월 이벤트 플래너입니다.",
    RESULT: "12월 26일에 우테코 식당에서 받을 이벤트 혜택 미리 보기!",
    ORDER_MENU: "\n<주문 메뉴>",
    TOTAL_PRICE: "\n<할인 전 총주문 금액>",
    GIFT_MENU: "\n<증정 메뉴>",
    EVENT_INFO: "\n<혜택 내역>",
    EVENT_PRICE: "\n<총혜택 금액>",
    AFTER_PRICE: "\n<할인 후 예상 결제 금액>",
    EVENT_BADGE: "\n<12월 이벤트 배지>",
    MENU_COUNT: (menu, count) => `${menu} ${count}개`,
    PRICE_WON: (price) => `${price}원`,
    EVENT_NAME_PRICE: (eventName, price) => `${eventName}: -${price}원`,
    NONE: "없음",
  },
});
export default gameMessage;
```

### 🎯 4. errorMessage.js

```jsx
const errorMessage = Object.freeze({
  INVALID_DATE: "[ERROR] 유효하지 않은 날짜입니다. 다시 입력해 주세요.\n",
  INVALID_ORDER: "[ERROR] 유효하지 않은 주문입니다. 다시 입력해 주세요.\n",
  INVALID_ORDER_ONLY_DRINK:
    "[ERROR] 음료만 주문할 수 없습니다. 다시 입력해 주세요.\n",
  INVALID_ORDER_LIMIT:
    "[ERROR] 메뉴는 20개까지만 주문할 수 있습니다. 다시 입력해 주세요.\n",
});
export default errorMessage;
```

## <mark style='background-color: #ffdce0'>2023.11.12(일)</mark>

### 🎯 5. 방문 날짜, 주문 입력

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>저번 과제에서 입력 부분의 유효성 검사를 어느 파일에서 하는 게 옳은지 해결하지 못 하여 mvc 패턴으로 구현하는 데에 어려움이 있었다. 이번엔 MainController안에 InputController를 만들어서 이 컨트롤러 안에서 입력과 유효성 검사를 하려고 한다.</div>
  </p>
</div>

- **views > InputView.js**

  ```jsx
  import { Console } from "@woowacourse/mission-utils";
  import gameMessage from "../constants/gameMessage.js";

  const InputView = {
    async readDate() {
      const input = await Console.readLineAsync(gameMessage.INPUT.DATE);
      return input;
    },
    async readOrder() {
      const input = await Console.readLineAsync(gameMessage.INPUT.ORDER);
      return input;
    },
  };

  export default InputView;
  ```

- **utils > Validate.js**
  <div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
    <p>
      <div>날짜가 바르게 입력되었는지?</div>
      <div>주문 입력이 공백인지?</div>
      <div>올바르게 입력 되었는지?</div>
      <div>기능 요구사항에 맞게 되었는지?</div>
    </p>
  </div>

  ```jsx
  import event from "../constants/event.js";
  import menu from "../constants/menuInfo.js";
  import errorMessage from "../constants/errorMessage.js";

  const Validate = {
    date(input) {
      this.dateNotNull(input);
      this.dateOnlyNumber(input);
      this.dateRange(input);
    },

    inputOrder(input) {
      this.orderNotNull(input);
    },

    orderItem(menu, quantity) {
      this.hyphen(quantity);
      this.menuName(menu);
      this.onlyNumber(quantity);
    },

    orderList(list) {
      this.notDuplicate(list);
      this.notOnlyDrink(list);
      this.limitQuantity(list);
    },

    dateNotNull(input) {
      if (!input.length) {
        throw new Error(errorMessage.INVALID_DATE);
      }
    },

    dateOnlyNumber(input) {
      const numberRegExp = /^[0-9]+$/;
      if (!numberRegExp.test(input)) {
        throw new Error(errorMessage.INVALID_DATE);
      }
    },

    dateRange(input) {
      if (
        !(Number(input) >= event.DAY.FIRST && Number(input) <= event.DAY.LAST)
      ) {
        throw new Error(errorMessage.INVALID_DATE);
      }
    },

    orderNotNull(input) {
      if (!input.length) {
        throw new Error(errorMessage.INVALID_ORDER);
      }
    },

    hyphen(input) {
      if (!input) {
        throw new Error(errorMessage.INVALID_ORDER);
      }
    },

    menuName(input) {
      if (!menu.hasOwnProperty(input)) {
        throw new Error(errorMessage.INVALID_ORDER);
      }
    },

    onlyNumber(input) {
      const numberRegExp = /^[0-9]+$/;
      if (!numberRegExp.test(input) || Number(input) < 1) {
        throw new Error(errorMessage.INVALID_ORDER);
      }
    },

    notDuplicate(orderList) {
      const orderSet = new Set(orderList.map(([menu]) => menu));
      if (orderSet.size !== orderList.length) {
        throw new Error(errorMessage.INVALID_ORDER);
      }
    },

    notOnlyDrink(orderList) {
      const hasNonDrinkItem = orderList.some(
        ([, , , , category]) => category !== "drink"
      );
      if (!hasNonDrinkItem) {
        throw new Error(errorMessage.INVALID_ORDER_ONLY_DRINK);
      }
    },

    limitQuantity(orderList) {
      const totalQuantity = orderList.reduce(
        (sum, [, , quantity]) => sum + quantity,
        0
      );
      if (totalQuantity > 20) {
        throw new Error(errorMessage.INVALID_ORDER_LIMIT);
      }
    },
  };

  export default Validate;
  ```

- **utils > CreateOrderList.js**
  <div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
    <p>
      <div>입력 유효성 검사를 충족시킨 후엔 CreateOrderList에서 배열로 만든 후 비즈니스 유효성 검사를 한다.</div>
      <div>이때 유효성 검사는 중복 제거, 음료만 주문 금지, 20개 이하로 주문 세 가지를 한다.</div>
    </p>
  </div>

  ```jsx
  import Validate from "./Validate.js";
  import menuInfo from "../constants/menuInfo.js";

  const CreateOrderList = (order) => {
    const items = order
      .replace(/\s/g, "")
      .split(",")
      .map((item) => item.trim());
    const orderList = items.filter(Boolean).map(parseItem);

    Validate.orderList(orderList);
    return orderList;
  };

  const parseItem = (item) => {
    const [menu, quantity] = item.split("-").map((part) => part.trim());
    Validate.orderItem(menu, quantity);

    const { price, category } = menuInfo[menu];
    const totalPrice = price * Number(quantity);
    return [menu, totalPrice, Number(quantity), price, category];
  };

  export default CreateOrderList;
  ```

- **controllers > InputController.js**
  <div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
    <p>
      <div>올바른 입력을 할 때까지 입력 받는 컨트롤러이다.</div>
    </p>
  </div>

  ```jsx
  import { Console } from "@woowacourse/mission-utils";
  import InputView from "../views/InputView.js";
  import Validate from "../utils/Validate.js";
  import CreateOrderList from "../utils/CreateOrderList.js";

  export default class InputController {
    async inputDate() {
      while (true) {
        const input = await InputView.readDate();
        try {
          Validate.date(input);
          return Number(input);
        } catch (error) {
          Console.print(error.message);
        }
      }
    }
    async inputOrder() {
      while (true) {
        const input = await InputView.readOrder();
        try {
          Validate.inputOrder(input);
          const orderList = CreateOrderList(input);
          return orderList;
        } catch (error) {
          Console.print(error.message);
        }
      }
    }
  }
  ```

### 🎯 6. 메인 컨트롤러

- **controllers > MainController.js**

  ```jsx
  import { Console } from "@woowacourse/mission-utils";
  import InputController from "./InputController.js";

  export default class MainController {
    #date;
    #order;

    async start() {
      await this.input();
    }

    async input() {
      const inputController = new InputController();
      this.#date = await inputController.inputDate();
      this.#order = await inputController.inputOrder();
    }
  }
  ```

### 🎯 7. 주문 총합 계산하기

- **controllers > MainController.js**
  <div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
    <p>
      <div>  총합 계산을 할인 전에 해야한다. 증정 이벤트에서 ‘총합이 12만원이 넘을 때’ 라는 조건이 있기 때문이다.</div>
    </p>
  </div>

  ```jsx
  import { Console } from "@woowacourse/mission-utils";
  import InputController from "./InputController.js";
  import CalculatelPrice from "../models/CalculatePrice.js";

  export default class MainController {
    #date;
    #order;
    #totalPrice;

    async start() {
      await this.input();
      await this.calculateTotalPrice();
    }

    async input() {
      const inputController = new InputController();
      this.#date = await inputController.inputDate();
      this.#order = await inputController.inputOrder();
    }

    async calculateTotalPrice() {
      this.#totalPrice = CalculatelPrice.total(this.#order);
    }
  }
  ```

- **models > CalculatePrice.js**

  ```jsx
  const CalculatelPrice = {
    total(input) {
      const totalSum = input.reduce((sum, [, price]) => sum + price, 0);
      return totalSum;
    },
  };

  export default CalculatelPrice;
  ```

## <mark style='background-color: #ffdce0'>2023.11.13(월)</mark>

### 🎯 8. 할인 계산하기

  <div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
    <p>
      <div>할인 이름과 가격이 담긴 배열을 resultList 배열에 넣는다. </div>
      <div>resultList는 이차원 배열이 되고 길이는 4이다. 0원이어도 resultList에 ['평일 할인', 0] 이런식으로 삽입된다.</div>
    </p>
  </div>

- **controllers > MainController.js**
  <div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
    <p>
      <div>만원 이상 주문 시에만 할인 메소드를 부르게 했다.</div>
    </p>
    <p>이 부분을 많이 고민했다. 그냥 무조건 메소드를 부른 후 모든 할인 금액이 0원인 resultList를 만들어야 할까 아니면 만원 이상 주문하지 않으면 메소드를 호출하지 않아 resultList를 빈 배열로 두어야할까</p>
    <p>결론은 후자로 했다. 첫 번째 이유는 문제에서 애초에 만원 이상일 때만 할인을 적용한다 하였기 때문에 그 문맥과 순서를 지키고 싶었고, 두 번째 이유는 EventDiscount 클래스에서 매 메소드마다 ‘만원이 넘지 않을때’ 라는 예외처리를 하는 것이 비효율적이라고 생각했기 때문이다.</p>
  </div>

  ```jsx
  import { Console } from "@woowacourse/mission-utils";
  import InputController from "./InputController.js";
  import event from "../constants/event.js";
  import EventDiscount from "../models/EventDiscount.js";
  import EventGift from "../models/EventGift.js";
  import CalculatelPrice from "../models/CalculatePrice.js";

  export default class MainController {
    #date;
    #order;
    #totalPrice;
    #resultList = [];
    #discountPrice = 0;

    async start() {
      await this.input();
      await this.calculateTotalPrice();
      if (this.#totalPrice >= event.DISCOUNT.CONDITION) {
        await this.discount();
        await this.calculateDiscountPrice();
      }
      await this.eventBadge();
    }

    async input() {
      const inputController = new InputController();
      this.#date = await inputController.inputDate();
      this.#order = await inputController.inputOrder();
    }

    async calculateTotalPrice() {
      this.#totalPrice = CalculatelPrice.total(this.#order);
    }

    async discount() {
      const eventDiscount = new EventDiscount(this.#date, this.#order);
      this.#resultList.push(eventDiscount.christmasDday());
      this.#resultList.push(eventDiscount.week());
      this.#resultList.push(eventDiscount.special());

      const eventGift = new EventGift(this.#totalPrice);
      this.#resultList.push(eventGift.result());
    }
  }
  ```

- **models > EventDiscount.js**
  <div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
    <p>
      <div>처음엔 이 클래스에 증정 이벤트 메소드도 있었다. 하지만 증정 이벤트는 주문 금액 총합만 필요했고, date와 order는 필요하지 않았기 때문에 이 메소드에 있기에는 저 두 인자를 사용하지 않아 적합하지 않다고 생각하였다.</div>
    </p>
  </div>

  ```jsx
  import event from "../constants/event.js";

  export default class EventDiscount {
    #date;
    #order;

    constructor(date, order) {
      this.#date = date;
      this.#order = order;
    }

    christmasDday() {
      const ddayDiscount =
        this.#date <= event.DAY.CHRISTMAS
          ? event.DISCOUNT.DDAY_INITIAL +
            event.DISCOUNT.DDAY_ADD * (this.#date - 1)
          : 0;

      return ["크리스마스 디데이 할인", ddayDiscount];
    }

    week() {
      let mainCount = 0;
      let dessertCount = 0;

      this.#order.forEach(([, , quantity, , category]) => {
        if (category === "main") {
          mainCount += quantity;
        }
        if (category === "dessert") {
          dessertCount += quantity;
        }
      });
      if (this.#date % 7 === 1 || this.#date % 7 === 2) {
        return ["주말 할인", event.DISCOUNT.WEEK * mainCount];
      }
      return ["평일 할인", event.DISCOUNT.WEEK * dessertCount];
    }

    special() {
      const specialDay = [3, 10, 17, 24, 25, 31];
      const specialDiscount = specialDay.includes(this.#date)
        ? event.DISCOUNT.SPECIAL
        : 0;

      return ["특별 할인", specialDiscount];
    }
  }
  ```

- **models > EventGift.js**
  <div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
    <p>
      <div>  추후에 주문 금액 총합을 출력해야 했기 때문에 EventDiscount의 order 인자로 주문 금액 총합을 계산하여 증정 이벤트 메소드를 하는 것은 계산을 두 번 하는 일이라고 생각하여 주문 금액 총합을 인자로 받는 EventGift를 따로 뺐다. MainController에서 주문 금액 총합을 계산하고 그 총합을 EventGift 메소드에 인자로 넘겨주었다.</div>
    </p>
  </div>

  ```jsx
  import event from "../constants/event.js";

  export default class EventGift {
    #price;

    constructor(price) {
      this.#price = price;
    }

    result() {
      const giftDiscount =
        this.#price >= event.GIFT.CONDITION ? event.GIFT.PRICE : 0;

      return ["증정 이벤트", giftDiscount];
    }
  }
  ```

### 🎯 9. 배지 계산하기

  <div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
    <p>
      <div>#discountPrice = 0; 으로 초기화 되어있고 총 주문 금액이 만원이 넘었을 때만 0이 다른 값으로 바뀐다. 이 할인 금액으로 배지를 계산한다.</div>
    </p>
  </div>

- **controllers > MainController.js**

  ```jsx
  import { Console } from "@woowacourse/mission-utils";
  import InputController from "./InputController.js";
  import event from "../constants/event.js";
  import EventDiscount from "../models/EventDiscount.js";
  import EventGift from "../models/EventGift.js";
  import CalculatelPrice from "../models/CalculatePrice.js";
  import EventBagde from "../models/EventBadge.js";

  export default class MainController {
    #date;
    #order;
    #totalPrice;
    #resultList = [];
    #discountPrice = 0;
    #badge;

    async start() {
      await this.input();
      await this.calculateTotalPrice();
      if (this.#totalPrice >= event.DISCOUNT.CONDITION) {
        await this.discount();
        await this.calculateDiscountPrice();
      }
      await this.eventBadge();
      await this.output();
    }

    async input() {
      const inputController = new InputController();
      this.#date = await inputController.inputDate();
      this.#order = await inputController.inputOrder();
    }

    async calculateTotalPrice() {
      this.#totalPrice = CalculatelPrice.total(this.#order);
    }

    async discount() {
      const eventDiscount = new EventDiscount(this.#date, this.#order);
      this.#resultList.push(eventDiscount.christmasDday());
      this.#resultList.push(eventDiscount.week());
      this.#resultList.push(eventDiscount.special());

      const eventGift = new EventGift(this.#totalPrice);
      this.#resultList.push(eventGift.result());
    }

    async calculateDiscountPrice() {
      this.#discountPrice = CalculatelPrice.total(this.#resultList);
    }

    async eventBadge() {
      this.#badge = new EventBagde(this.#discountPrice).result();
    }
  }
  ```

- **models > EventBadge.js**

  ```jsx
  import event from "../constants/event.js";
  import gameMessage from "../constants/gameMessage.js";

  export default class EventBagde {
    #price;

    constructor(price) {
      this.#price = price;
    }

    result() {
      const { STAR, TREE, SANTA } = event.BADGE;

      if (this.#price >= SANTA) return "산타";
      if (this.#price >= TREE) return "트리";
      if (this.#price >= STAR) return "별";

      return gameMessage.OUTPUT.NONE;
    }
  }
  ```

## <mark style='background-color: #ffdce0'>2023.11.14(화)</mark>

### 🎯 10. 결괏값 출력하기

- **controllers > MainController.js**

  ```jsx
  import { Console } from "@woowacourse/mission-utils";
  import OutputView from "../views/OutputView.js";
  import InputController from "./InputController.js";
  import event from "../constants/event.js";
  import EventDiscount from "../models/EventDiscount.js";
  import EventGift from "../models/EventGift.js";
  import CalculatelPrice from "../models/CalculatePrice.js";
  import EventBagde from "../models/EventBadge.js";

  export default class MainController {
    #date;
    #order;
    #totalPrice;
    #resultList = [];
    #discountPrice = 0;
    #badge;

    async start() {
      OutputView.greeting();
      await this.input();
      await this.calculateTotalPrice();
      if (this.#totalPrice >= event.DISCOUNT.CONDITION) {
        await this.discount();
        await this.calculateDiscountPrice();
      }
      await this.eventBadge();
      await this.output();
    }

    async input() {
      const inputController = new InputController();
      this.#date = await inputController.inputDate();
      this.#order = await inputController.inputOrder();
    }

    async calculateTotalPrice() {
      this.#totalPrice = CalculatelPrice.total(this.#order);
    }

    async discount() {
      const eventDiscount = new EventDiscount(this.#date, this.#order);
      this.#resultList.push(eventDiscount.christmasDday());
      this.#resultList.push(eventDiscount.week());
      this.#resultList.push(eventDiscount.special());

      const eventGift = new EventGift(this.#totalPrice);
      this.#resultList.push(eventGift.result());
    }

    async calculateDiscountPrice() {
      this.#discountPrice = CalculatelPrice.total(this.#resultList);
    }

    async eventBadge() {
      this.#badge = new EventBagde(this.#discountPrice).result();
    }

    async output() {
      OutputView.result();
      OutputView.orderMenu(this.#order);
      OutputView.totalPrice(this.#totalPrice);
      OutputView.giftMenu(this.#resultList);
      OutputView.eventInfo(this.#resultList);
      OutputView.eventPrice(this.#discountPrice);
      OutputView.afterPrice(
        this.#resultList,
        this.#totalPrice,
        this.#discountPrice
      );
      OutputView.eventBadge(this.#badge);
    }
  }
  ```

- **views > OutputView.js**

  ```jsx
  import { Console } from "@woowacourse/mission-utils";
  import gameMessage from "../constants/gameMessage.js";
  import PriceFormat from "../utils/PriceFormat.js";
  import event from "../constants/event.js";

  const OutputView = {
    greeting() {
      Console.print(gameMessage.OUTPUT.GREETING);
    },

    result() {
      Console.print(gameMessage.OUTPUT.RESULT);
    },

    orderMenu(order) {
      Console.print(gameMessage.OUTPUT.ORDER_MENU);
      order.forEach(([menu, , quantity]) => {
        Console.print(gameMessage.OUTPUT.MENU_COUNT(menu, quantity));
      });
    },

    totalPrice(price) {
      Console.print(gameMessage.OUTPUT.TOTAL_PRICE);
      Console.print(gameMessage.OUTPUT.PRICE_WON(PriceFormat(price)));
    },

    giftMenu(result) {
      Console.print(gameMessage.OUTPUT.GIFT_MENU);
      !result.length || !result[3][1]
        ? Console.print(gameMessage.OUTPUT.NONE)
        : Console.print(gameMessage.OUTPUT.MENU_COUNT("샴페인", 1));
    },

    eventInfo(result) {
      Console.print(gameMessage.OUTPUT.EVENT_INFO);
      if (!result.length) {
        Console.print(gameMessage.OUTPUT.NONE);
      }
      result.forEach(([eventName, price]) => {
        if (price) {
          Console.print(
            gameMessage.OUTPUT.EVENT_NAME_PRICE(eventName, PriceFormat(price))
          );
        }
      });
    },

    eventPrice(price) {
      Console.print(gameMessage.OUTPUT.EVENT_PRICE);
      const formattedPrice = PriceFormat(price);
      Console.print(
        gameMessage.OUTPUT.PRICE_WON(
          price > 0 ? "-" + formattedPrice : formattedPrice
        )
      );
    },

    afterPrice(result, total, discount) {
      let price = total - discount;
      if (result.length && result[3][1]) {
        price += event.GIFT.PRICE;
      }
      Console.print(gameMessage.OUTPUT.AFTER_PRICE);
      Console.print(gameMessage.OUTPUT.PRICE_WON(PriceFormat(price)));
    },

    eventBadge(badge) {
      Console.print(gameMessage.OUTPUT.EVENT_BADGE);
      Console.print(badge);
    },
  };

  export default OutputView;
  ```

### ✅ 테스트

- **InputTest.js**
  <div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
    <p>
      <div>날짜와 메뉴를 입력하는 테스트 코드다.</div>
    </p>
  </div>

  ```jsx
  import InputController from "../src/controllers/InputController";
  import { Console } from "@woowacourse/mission-utils";

  describe("입력 테스트", () => {
    let inputController;

    beforeEach(() => {
      inputController = new InputController();
    });

    test("유효한 날짜 입력", async () => {
      const mockConsoleReadLineAsync = jest.fn(() => Promise.resolve("3"));
      jest
        .spyOn(Console, "readLineAsync")
        .mockImplementation(mockConsoleReadLineAsync);

      const input = await inputController.inputDate();

      expect(input).toEqual(3);
      expect(mockConsoleReadLineAsync).toHaveBeenCalled();
    });

    test("유효하지 않은 날짜 입력 후 재시도", async () => {
      const mockConsoleReadLineAsync = jest
        .fn()
        .mockImplementationOnce(() => Promise.resolve(""))
        .mockImplementationOnce(() => Promise.resolve("df"))
        .mockImplementationOnce(() => Promise.resolve("0"))
        .mockImplementationOnce(() => Promise.resolve("32"))
        .mockImplementationOnce(() => Promise.resolve("5"));
      jest
        .spyOn(Console, "readLineAsync")
        .mockImplementation(mockConsoleReadLineAsync);

      const input = await inputController.inputDate();

      expect(input).toEqual(5);
      expect(mockConsoleReadLineAsync).toHaveBeenCalledTimes(5);
    });

    test("유효한 메뉴 입력", async () => {
      const mockConsoleReadLineAsync = jest.fn(() =>
        Promise.resolve("타파스-1,제로콜라-1")
      );
      jest
        .spyOn(Console, "readLineAsync")
        .mockImplementation(mockConsoleReadLineAsync);

      const input = await inputController.inputOrder();

      expect(input).toEqual([
        ["타파스", 5500, 1, 5500, "appetizer"],
        ["제로콜라", 3000, 1, 3000, "drink"],
      ]);
      expect(mockConsoleReadLineAsync).toHaveBeenCalled();
    });

    test("유효하지 않은 메뉴 입력 후 재시도", async () => {
      const mockConsoleReadLineAsync = jest
        .fn()
        .mockImplementationOnce(() => Promise.resolve(""))
        .mockImplementationOnce(() => Promise.resolve("타파스*1,제로콜라=1"))
        .mockImplementationOnce(() => Promise.resolve("타파스-1,펩시콜라-1"))
        .mockImplementationOnce(() => Promise.resolve("타파스-0,제로콜라-1"))
        .mockImplementationOnce(() => Promise.resolve("타파스-1,타파스-1"))
        .mockImplementationOnce(() => Promise.resolve("제로콜라-1,레드와인-1"))
        .mockImplementationOnce(() =>
          Promise.resolve(
            "시저샐러드-5, 티본스테이크-5, 크리스마스파스타-8, 제로콜라-3, 아이스크림-4"
          )
        )
        .mockImplementationOnce(() => Promise.resolve("타파스-1,제로콜라-1"));

      jest
        .spyOn(Console, "readLineAsync")
        .mockImplementation(mockConsoleReadLineAsync);

      const input = await inputController.inputOrder();

      expect(input).toEqual([
        ["타파스", 5500, 1, 5500, "appetizer"],
        ["제로콜라", 3000, 1, 3000, "drink"],
      ]);
      expect(mockConsoleReadLineAsync).toHaveBeenCalledTimes(8);
    });
  });
  ```

- **EventDiscountTest.js**
  <div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
    <p>
      <div>날짜와 주문에 따른 결괏값을 테스트한다.</div>
    </p>
  </div>

  ```jsx
  import EventDiscount from "../src/models/EventDiscount";

  describe("할인 이벤트 테스트", () => {
    test.each`
      date  | expected
      ${10} | ${["크리스마스 디데이 할인", 1900]}
      ${25} | ${["크리스마스 디데이 할인", 3400]}
      ${26} | ${["크리스마스 디데이 할인", 0]}
    `("크리스마스 디데이 할인 테스트 - date: $date", ({ date, expected }) => {
      const order = [
        ["티본스테이크", 55000, 1, 55000, "main"],
        ["바비큐립", 54000, 1, 54000, "main"],
        ["초코케이크", 30000, 3, 15000, "dessert"],
        ["제로콜라", 3000, 1, 3000, "drink"],
      ];
      const eventDiscount = new EventDiscount(date, order);
      const result = eventDiscount.christmasDday();
      expect(result).toEqual(expected);
    });

    test.each`
      date  | expected
      ${8}  | ${["주말 할인", 4046]}
      ${19} | ${["평일 할인", 6069]}
    `("평일 할인, 주말 할인 테스트 - date: $date", ({ date, expected }) => {
      const order = [
        ["티본스테이크", 55000, 1, 55000, "main"],
        ["바비큐립", 54000, 1, 54000, "main"],
        ["초코케이크", 30000, 3, 15000, "dessert"],
        ["제로콜라", 3000, 1, 3000, "drink"],
      ];
      const eventDiscount = new EventDiscount(date, order);
      const result = eventDiscount.week();
      expect(result).toEqual(expected);
    });

    test.each`
      date | expected
      ${3} | ${["특별 할인", 1000]}
      ${4} | ${["특별 할인", 0]}
    `("특별 할인 테스트 - date: $date", ({ date, expected }) => {
      const order = [
        ["티본스테이크", 55000, 1, 55000, "main"],
        ["바비큐립", 54000, 1, 54000, "main"],
        ["초코케이크", 30000, 3, 15000, "dessert"],
        ["제로콜라", 3000, 1, 3000, "drink"],
      ];
      const eventDiscount = new EventDiscount(date, order);
      const result = eventDiscount.special();
      expect(result).toEqual(expected);
    });
  });
  ```

- **EventGiftTest.js**
  <div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
    <p>
      <div>총 주문 금액에 따른 결괏값을 테스트한다.</div>
    </p>
  </div>

  ```jsx
  import EventGift from "../src/models/EventGift";

  describe("증정 이벤트 테스트", () => {
    test.each`
      totalPrice | expected
      ${130000}  | ${["증정 이벤트", 25000]}
      ${109000}  | ${["증정 이벤트", 0]}
      ${150000}  | ${["증정 이벤트", 25000]}
      ${5000}    | ${["증정 이벤트", 0]}
    `("totalPrice: $totalPrice", ({ totalPrice, expected }) => {
      const eventDiscount = new EventGift(totalPrice);
      const result = eventDiscount.result();
      expect(result).toEqual(expected);
    });
  });
  ```

- **EventBadgeTest.js**
    <div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
      <p>
        <div>할인 금액에 따른 결괏값을 테스트한다.</div>
      </p>
    </div>

  ```jsx
  import EventBagde from "../src/models/EventBadge";

  describe("EventBadge 클래스 테스트", () => {
    test.each`
      price    | expected
      ${5000}  | ${"별"}
      ${19000} | ${"트리"}
      ${20000} | ${"산타"}
      ${100}   | ${"없음"}
    `("result 함수 테스트 - price: $price", ({ price, expected }) => {
      const eventBadge = new EventBagde(price);
      const result = eventBadge.result();
      expect(result).toEqual(expected);
    });
  });
  ```

## <mark style='background-color: #ffdce0'>2023.11.15(수)</mark>

**과제 소감**

저번 과제에서 mvc 패턴로 구현하다가 포기했었는데 이번엔 view 파일 두 개가 있었고 문제를 읽을 후 mvc 패턴이 가장 적합하다고 판단하여 mvc 패턴을 정확히 공부하고 구현하였다. mvc 패턴이 이론적으로 어려웠는데 model이 백엔드 내부코드, controller가 api 호출, view가 프론트라고 생각하니 이해가 조금 쉬워졌다.

저번 과제에서 실패했던 부분을 InputController에서 입력과 유효성 검사를 처리하여 해결하였다. 그동안 했던 과제와 다르게 한 점은 띄어쓰기와 쉼표의 오타를 허용하는 것이었다. 그렇게 안 하면 이미 예외 처리가 7개나 있었기 때문에 사용자에게 너무 많은 피로도를 줄 수 있을 거라고 생각했다.

처음의 할인 이벤트 클래스는 날짜와 주문을 인자로 받아 4개의 할인 이벤트 결과를 반환하는 클래스였다. 하지만 증정 이벤트는 날짜와 주문 필드를 전혀 사용하고 있지 않았기 때문에 리팩토링을 하면서 증정 이벤트 클래스를 독립적으로 뺐다.

그리고 가장 많이 고민했던 부분은 혜택 내역을 배열로 받을 것인지, 객체로 받을 것인지와 0이면 결과 리스트에 추가하지 않을 것인지였다. 요소가 많아지면 객체로 받으려고 했지만 이벤트 이름과 할인 금액 두 개밖에 없었기 때문에 배열로 받았다. 또 증정 이벤트의 가격을 result[3][1]로 지정해두었기 때문에 0원도 결과 리스트에 추가하였다.

미션을 수행하면서 가장 많은 리팩토링을 하였는데 조건 처리가 많고 피드백을 수용하려다 보니 처음 코드에서 계속 개선할 점을 찾았던 것 같다. 그동안 받았던 피드백을 리액트에도 적용하여 이 프리코스가 더 나은 코드를 짜기 위한 발판이 되길 바란다.
