---
layout: single
title: "[우테코]Level1 점심 뭐 먹지 2단계"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션3 🥡점심 뭐 먹지 2단계

## <mark class="pink">🔥2단계 구현 사항</mark>

[2단계 - 자주 가는 음식점](https://github.com/woowacourse/javascript-lunch/pull/159){: .btn .btn--primary}

[점심 뭐 먹지 배포 링크](https://chlwlstlf.github.io/javascript-lunch/dist/){: .btn .btn--primary}

### <mark class="yellow">1. DOM 트리 조작</mark>

아래와 같이 요소를 추가나 교체할 수 있다.

```ts
parent.appendChild(child); // 자식 요소 추가
parent.prepend(child); // 첫 번째 자식으로 추가
parent.replaceChildren(); // 모든 자식 요소 제거
parent.replaceChildren(newChild); // 모든 자식 요소를 새로운 요소로 교체
```

index.html에 하드 코딩을 모두 제거하고 컴포넌트를 삽입하자.

```html
<div id="container">
  <section class="restaurant-list-container"></section>
</div>
```

<br>

**1\. Header - prepend**

`header`는 `container`의 첫 번째 자식 요소로 추가되어야 한다. 따라서 `prepend`로 추가했다.

```ts
renderHeader(container) {
  const header = Header({
    title: '점심 뭐 먹지',
    imageSource: './add-button.png',
    onClick: () => {
      this.#addRestaurantModal.open();
      this.manageAddRestaurantFormEvents();
    },
  });

  container.prepend(header);
}
```

<br>

**2\. 식당 리스트 - replaceChildren**

즐겨찾기나 카테고리, 정렬 등 processedList에 변화가 생기면 화면에 보이는 식당 리스트가 바뀌어야 한다.  
`<ul>` 태그 안의 `<li>`들을 바꾸어야 하기 떄문에 `...restaurantItems`로 replaceChildren을 해주어야 한다. replaceChildren()은 여러 개의 노드를 한 번에 받을 수 있는 메서드이기 때문에 스프레드 연산자를 사용하여 여러 개 노드를 인자로 전달할 수 있다.

```ts
getRestaurantItemsFragment(processedList) {
  const listFragment = document.createElement('ul');
  listFragment.classList.add('restaurant-list');

  const container = $('#container');

  const restaurantItems = processedList.map(restaurantItem =>
    RestaurantItem({
      // 코드 생략
    }),
  );

  listFragment.replaceChildren(...restaurantItems);
  return listFragment;
}
```

<br>

💡 스프레드 언제 사용할까?  
아이템을 개별 요소로 바꿀 때 사용된다. forEach 루프를 제거하고 간결하게 표현할 수 있다.

```ts
const restaurantItems = [item1, item2, item3];

// 1. 스프레드 연산자 미사용
someFunction(restaurantItems);
// 결과: 함수가 배열 하나를 인자로 받음
// someFunction([item1, item2, item3])

// 2. 스프레드 연산자 사용
someFunction(...restaurantItems);
// 결과: 배열이 개별 요소로 펼쳐져서 전달됨
// someFunction(item1, item2, item3)
```

<br>

**배열 스프레드 연산자**

1\. 배열 합치기

```ts
const arr1 = [1, 2];
const arr2 = [3, 4];
const combined = [...arr1, ...arr2]; // [1, 2, 3, 4]
```

※ `+`로 배열을 합칠 수 없다.

```ts
console.log(arr1 + arr2);
// 결과: "1,23,4" (문자열로 변환되어 연결됨)
```

---

2\. 배열 복사

```ts
const original = [1, 2, 3];
const copy = [...original]; // [1, 2, 3]
```

<br>

**객체 스프레드 연산자**

1\. 객체 펼치기

```ts
const restaurant = { name: "식당A", type: "한식" };
const restaurantWithId = {
  id: 1,
  ...restaurant,
}; // { id: 1, name: '식당A', type: '한식' }
```

---

※ 객체는 객체 리터럴({ }) 밖에서 단독으로 사용할 수 없다.

객체 리터럴 내부 { ... }, 배열 리터럴 내부 [ ... ], 함수 호출/정의에서 나머지 매개변수({ name, ...rest })로 사용 시에만 사용 가능하다.

```ts
console.log(...restaurant);
// SyntaxError: Spread syntax requires ...iterable object

function printProps(name, type) {
  console.log(name, type);
}
printProps(...restaurant); // 에러 발생

console.log({ ...restaurant });
// 결과: { name: '식당A', type: '한식' }
```

---

※ 반면 객체는 컨텍스트 제약이 없다.

```ts
const arr = [1, 2, 3];
console.log(...arr); // 1 2 3
```

<br>
<br>

### <mark class="yellow">모달 super</mark>

**src/components/Modal/Modal.ts**

```ts
interface Props {
  child: HTMLElement;
}

class Modal {
  #modal = document.createElement("div");
  #modalBackdrop = document.createElement("div");
  #modalContainer = document.createElement("div");

  constructor({ child }: Props) {
    this.#modal.classList.add("modal");
    this.#modalBackdrop.classList.add("modal-backdrop");
    this.#modalContainer.classList.add("modal-container");

    this.#modalContainer.appendChild(child);
    this.#modal.appendChild(this.#modalBackdrop);
    this.#modal.appendChild(this.#modalContainer);

    this.#modalBackdrop.addEventListener("click", this.close.bind(this));
  }

  get element() {
    return this.#modal;
  }

  open() {
    this.#modal.classList.add("modal--open");
  }

  close() {
    this.#modal.classList.remove("modal--open");
  }
}

export default Modal;
```

<br>

**1. element getter 사용**

#modal은 private field로 정의되어 클래스 외부에서 직접 접근할 수 없다.

getter를 통해 #modal을 읽을 수 있도록 외부에 노출한다. getter를 사용하면 코드에서 modalInstance.element처럼 깔끔하게 접근 가능하며, 특정 논리를 추가하거나 캡슐화를 유지하는 데 유용하다.

<br>

**2. bind(this)**

Modal에 아래와 같은 코드가 있다. `this.close` 뒤에 `.bind(this)`가 사용되었다. 왜 일까?

```ts
this.#modalBackdrop.addEventListener("click", this.close.bind(this));
```

<br>

addEventListener에 전달하는 콜백 함수(this.close)는 호출 시 기본적으로 this가 이벤트를 발생시킨 요소를 참조한다. 즉 여기서 this는 `#modalBackdrop`을 의미한다.  
`#modalBackdrop`에는 close라는 메소드가 없으므로 this가 가리키는 것을 바꿔야한다. `this.close`를 **클래스의 close 메서드**로 유지하려면 명시적으로 bind를 사용해 this를 **현재 클래스 인스턴스로 고정**해야 한다.

<br>

💡 bind를 사용하지 않으려면 화살표 함수로 this를 고정할 수 있다.

```ts
this.#modalBackdrop.addEventListener("click", () => this.close());
```

<br>

**3. super 사용**

`super`는 자식 클래스에서 부모 클래스의 생성자나 메서드를 호출할 때 사용하는 키워드이다.

**src/components/AddRestaurantModal/AddRestaurantModal.ts**

AddingRestaurantModal이 Modal 클래스를 상속하고 있으므로, 부모 클래스의 생성자(Modal)를 호출해야 한다.

Modal 클래스는 생성자에서 child를 받아 #modal 내부 구조를 초기화한다. 이를 통해 AddingRestaurantModal이 Modal의 모든 초기화 로직을 상속받을 수 있다.

```ts
const fragment = document.createDocumentFragment();
// 식당 추가 모달 코드 생략
const addingModalLayout = document.createElement("div");
addingModalLayout.appendChild(fragment);

class AddingRestaurantModal extends Modal {
  constructor() {
    super({ child: addingModalLayout });
    form.addEventListener("reset", () => this.close());
  }
}

export default AddingRestaurantModal;
```

<br>

**[동작 원리]**

1\. `AddingRestaurantModal`이 인스턴스화될 때 먼저 `super()`를 호출한다.
2\. `super()`는 부모 클래스인 `Modal`의 생성자를 실행한다.
3\. `{ child: addingModalLayout }`을 인자로 전달하여 모달의 내용을 설정한다.
4\. 부모 클래스 생성자 실행이 완료된 후, 자식 클래스의 나머지 초기화 코드가 실행된다.

만약 `super()`를 호출하지 않으면 에러가 발생한다. 이는 자바스크립트에서 상속을 사용할 때 부모 클래스의 초기화가 반드시 필요하기 때문이다.

<br>

<div class="blue-box">
  <b>💡 왜 child를 super로 넘겨야 할까?</b>
  <div>Modal은 child를 받아 내부의 #modalContainer에 추가한다.</div>
  <div>AddingRestaurantModal의 UI 레이아웃(addingModalLayout)을 child로 넘겨주어 부모 클래스에서 해당 UI를 관리하도록 한다.</div>
  <div>Modal의 재사용성을 높이고, 모든 모달의 기본 동작(open, close)을 공유할 수 있다.</div>
</div>

<br>
<br>

## <mark class="pink">🔥2단계 피드백</mark>

### <mark class="yellow">1. 매번 새로운 모달 띄우기</mark>

**[이전 코드]**

App 시작시 상세 모달 인스턴스를 생성하고, 식당 아이템을 클릭하면 이미 생성된 상세 모달에서 식당 내용만 변경하여 보여주는 형태이다.

```ts
class App {
  #detailRestaurantModal;

  constructor() {
    this.#detailRestaurantModal = new DetailRestaurantModal(); // 모달 인스턴스 생성
  }

  run() {
    const container = $("#container");
    container.appendChild(this.#detailRestaurantModal.element);
  }

  getRestaurantItemsFragment(processedList) {
    const listFragment = document.createElement("ul");
    listFragment.classList.add("restaurant-list");

    const container = $("#container");

    const restaurantItems = processedList.map((restaurantItem) =>
      RestaurantItem({
        restaurant: restaurantItem,
        onItemClick: () => {
          this.#detailRestaurantModal.restaurant = restaurantItem; // 원래 있던 모달 열기
          this.#detailRestaurantModal.open();
          this.manageDetailRestaurantEvents(restaurantItem);
        },
        onFavoriteImageClick: (event) => {
          this.handleButtonChange(event, restaurantItem);
        },
      })
    );

    listFragment.replaceChildren(...restaurantItems);
    return listFragment;
  }
}
```

<video controls>
  <source src="https://github.com/user-attachments/assets/e68f4d5b-d925-4049-bb03-de6c3702820b" type="video/mp4">
</video>

<br>

**[피드백]**

각 restaurant마다 DetailRestaurantModal을 갖고 있어야 하는데, 지금 가장 루트의 App에서 DetailRestaurantModal을 하나 띄우고 서로 다른 restaurant을 선택할 때마다 내용을 갈아끼워주는 구조이다.

이렇게 미리 정적인 방식으로 DetailRestaurantModal을 선언하고 내용을 갈아끼우는 방식은 매우 위험하며 비효율적이다. 언제 어디서 앱 전체에 하나뿐인 DetailRestaurantModal을 건드릴지 모르기 때문이다.

매번 선택한 restaurant에 해당하는 DetailRestaurantModal을 띄워주는 구조로 변경해야 한다.

<br>

**[질문 사항]**

모달을 클래스로 만든 후 아이템을 누를 때마다 생성하면 누른 아이템 수만큼 html에 모달 코드가 추가된다. 모달은 그대로 두고 안에 내용만 갈아 끼우는 것이 아니라 새로운 모달을 추가하는 것이 일반적인지 혹은 메모리나 렌더링 부분에서 괜찮은 방법인지 궁금하다.

<br>

**[답변]**

모달을 닫을 때, close 동작에서 현재 모달의 html을 제거해주는 코드를 추가해야 한다. 리액트에서는 이 과정을 리액트가 알아서 해주지만, 순수 자바스크립트로 작성할 때는 이 과정을 개발자가 직접해주어야 하기 때문이다.

단순히 이 정도의 HTML을 추가하고 제거하는 것은 성능 측면에서 문제가 되진 않는다. (내용만 갈아끼우기 vs 삭제 후 재 렌더링 사이에 큰 차이가 없을 것으로 보인다)

<br>

**[수정한 코드]**

**src/components/Modal/Modal.ts**

모달 닫을 때 DOM에서 해당 요소를 제거한다.

```ts
interface Props {
  child: HTMLElement;
}

class Modal {
  // 코드 생략
  close() {
    this.#modal.classList.remove("modal--open");
    this.#modal.remove(); // DOM에서 모달 요소 제거
    this.#cleanup(); // 메모리 누수 방지
  }

  #cleanup() {
    this.#modalBackdrop.removeEventListener("click", () => this.close());
  }
}

export default Modal;
```

**App.js**

```ts
class App {
  getRestaurantItemsFragment(processedList) {
    const listFragment = document.createElement("ul");
    listFragment.classList.add("restaurant-list");

    const container = $("#container");

    const restaurantItems = processedList.map((restaurantItem) =>
      RestaurantItem({
        restaurant: restaurantItem,
        onItemClick: () => {
          // 아이템 클릭할 때마다 새로운 모달 생성
          const detailRestaurant = new DetailRestaurantModal(restaurantItem);
          container.appendChild(detailRestaurant.element);
          detailRestaurant.open();
          this.manageDetailRestaurantEvents(detailRestaurant, restaurantItem);
        },
        onFavoriteImageClick: (event) => {
          this.handleFavoriteButtonToggle(event, restaurantItem);
        },
      })
    );

    listFragment.replaceChildren(...restaurantItems);
    return listFragment;
  }
}
```

<video controls>
  <source src="https://github.com/user-attachments/assets/3c04bce5-c6fc-4308-a91c-4f6dd31b4f0b" type="video/mp4">
</video>

<br>
<br>

### <mark class="yellow">2. button 태그에 onClick 달기</mark>

**[이전 코드]**

**src/components/Common/FavoriteButton.ts**

컴포넌트 이름은 Button인데 img를 return하고 있고 button 역할을 하고 있다.

```ts
const FavoriteButton = ({ favorite }: Props) => {
  const img = document.createElement("img");
  img.classList.add("favorite-button");
  img.src = favorite
    ? "./favorite-icon-filled.svg"
    : "./favorite-icon-lined.svg";
  img.alt = "사진";

  return img;
};
```

<br>

**[피드백]**

지금 `<img>` 태그에 click 이벤트가 붙어있었는데, img는 원래 clickable한 요소가 아니므로 semantic HTML을 위해서는 `<button>` 태그를 사용해야 한다.

<br>

**[수정한 코드]**

**src/components/Common/FavoriteButton.ts**

img를 button으로 감싸고 그 button을 반환했다.

```ts
const FavoriteButton = (favorite: boolean) => {
  const button = document.createElement("button");
  button.classList.add("favorite-button");

  const img = document.createElement("img");
  img.src = favorite
    ? "./favorite-icon-filled.svg"
    : "./favorite-icon-lined.svg";
  img.alt = "사진";

  button.appendChild(img);
  return button;
};

export default FavoriteButton;
```

<br>

**src/components/Common/Common.css**

button은 기본 버튼 스타일이 있기 때문에 border, background, outline을 없애주어야 한다.

```css
.favorite-button {
  border: none;
  background: transparent;
  outline: none;
  cursor: pointer;
}
```

<br>
<br>

### <mark class="yellow">3. event.stopPropagation()</mark>

**[틀린 코드]**

event.stopPropagation()은 이벤트 버블링을 중단시키는 역할을 한다.  
이 코드 구조에서는 onFavoriteButtonClick 호출이 우선 실행되므로 onFavoriteButtonClick 내부에서 추가적인 이벤트를 트리거하거나 다른 리스너를 호출한다면, 예상치 못한 이벤트 전파가 발생할 수 있다.

```ts
if (onFavoriteButtonClick) {
  onFavoriteButtonClick(event);
  event.stopPropagation();
}
```

<br>

**[수정한 코드]**

event.stopPropagation()을 먼저 호출
이벤트 버블링을 먼저 중단시킨 다음, 콜백을 실행하면 의도치 않은 이벤트 전파를 방지할 수 있다.

```ts
if (onFavoriteButtonClick) {
  onFavoriteButtonClick(event);
  event.stopPropagation();
}
```
