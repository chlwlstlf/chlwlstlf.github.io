---
layout: single
title: "[우테코]Level1 점심 뭐 먹지 1단계"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션3 🥡점심 뭐 먹지 1단계

## <mark class="pink">🔥1단계 구현 사항</mark>

[1단계 - 음식점 목록](https://github.com/woowacourse/javascript-lunch/pull/128){: .btn .btn--primary}

### <mark class="yellow">1. RestaurantService</mark>

**src/interface/RestaurantInterfaces.ts**

레스토랑 도메인의 인터페이스 코드이다. 이 타입들로 레스토랑 도메인을 만들었다.

```ts
type Category = "한식" | "중식" | "일식" | "아시안" | "양식" | "기타";

type FilteringCategory = "전체" | Category;

type Distance = 5 | 10 | 15 | 20 | 30;

type SortingProperty = "name" | "distance";

interface Restaurant {
  category: Category;
  name: string;
  distance: Distance;
  description?: string;
  link?: string;
}

interface Restaurants {
  addRestaurant: (
    restaurant: Restaurant,
    restaurantList: Restaurant[]
  ) => boolean;
  filterByCategory: (
    category: Category,
    restaurantList: Restaurant[]
  ) => Restaurant[];
  sortByProperty: (
    property: SortingProperty,
    restaurantList: Restaurant[]
  ) => Restaurant[];
}

export {
  Category,
  FilteringCategory,
  Distance,
  SortingProperty,
  Restaurant,
  Restaurants,
};
```

<br>

**src/domain/RestaurantService.ts**

클래스에서 인터페이스는 `implements`를 사용해서 타입을 지정할 수 있다.

```ts
import { LOCALSTORAGE_KEY } from "../constant/constants";
import {
  FilteringCategory,
  SortingProperty,
  Restaurant,
  Restaurants,
} from "../interface/RestaurantInterfaces";

class RestaurantService implements Restaurants {
  addRestaurant(restaurant: Restaurant, restaurantList: Restaurant[]): boolean {
    const existingRestaurant = restaurantList.find(
      (item) =>
        item.category === restaurant.category && item.name === restaurant.name
    );
    if (existingRestaurant) {
      return false;
    }
    restaurantList.push(restaurant);
    localStorage.setItem(
      LOCALSTORAGE_KEY.RESTAURANT_LIST,
      JSON.stringify(restaurantList)
    );
    return true;
  }

  filterByCategory(
    category: FilteringCategory,
    restaurantList: Restaurant[]
  ): Restaurant[] {
    if (category === "전체") return restaurantList;
    return restaurantList.filter(
      (restaurant) => restaurant.category === category
    );
  }

  sortByProperty(
    property: SortingProperty,
    restaurantList: Restaurant[]
  ): Restaurant[] {
    return restaurantList.sort((a: Restaurant, b: Restaurant) =>
      a[property] > b[property] ? 1 : -1
    );
  }
}

export default RestaurantService;
```

<br>
<br>

### <mark class="yellow">2. 버튼 컴포넌트</mark>

**src/components/Common/Button.ts**

공통 버튼 컴포넌트를 만들었다.

```ts
const Button = (
  type: string,
  variant: "primary" | "secondary",
  content: string
) => {
  return /*html*/ `
    <button type=${type} class="button button--${variant} text-caption">${content}</button>
  `;
};

export default Button;
```

<br>

**src/components/Modal/AddRestaurant.ts**

Button이 JavaScript 함수라면 `${}`를 사용하여 버튼 컴포넌트에서 반환된 HTML 문자열을 템플릿 리터럴에 삽입할 수 있다.

```js
const AddRestaurant = () => {
  return /*html*/ `
    <div class="button-container">
      ${Button("reset", "secondary", "취소하기")}
      ${Button("submit", "primary", "추가하기")}
    </div>
  `;
};
```

<br>
<br>

### <mark class="yellow">3. insertAdjacentHTML</mark>

**index.html**

`index.html`에 뼈대 코드가 있다. 이 안에 필터 드롭다운 부분을 추가하려고 한다.

```html
<!-- 카테고리/정렬 필터 -->
<section class="restaurant-filter-container"></section>
```

<br>

**src/controllers/RestaurantController.js**

`insertAdjacentHTML`라는 Web API를 사용하여 `filterContainer`의 마지막 자식 요소 뒤에 `filterDropdown`와 `sortDropdown`를 차례대로 추가한다.

```js
showFilterDropdown() {
  const filterContainer = $('.restaurant-filter-container');

  const filterDropdown = Dropdown(FILTER_DROPDOWN_PROPS);
  const sortDropdown = Dropdown(SORT_DROPDOWN_PROPS);

  filterContainer.insertAdjacentHTML('beforeend', filterDropdown);
  filterContainer.insertAdjacentHTML('beforeend', sortDropdown);
}
```

<br>
<br>

## <mark class="pink">🔥1단계 피드백</mark>

### <mark class="yellow">1. 확정 할당 어선셜</mark>

**[질문 사항]**

View와 Controller를 JS로 구현했다.  
TS에서 undefined나 null 일 수 있다는 에러가 떠서 `확정 할당 연산자('!')`를 사용할까 했지만 모든 querySelector에 '!'를 남발하는 것 같아서 JS로 바꿨다. 만약 후에 TS를 쓰게 되면 이 부분에 '!'를 쓰는 게 맞을까?

```ts
const form = document.querySelector("form")!;
```

<br>

**[추가 공부]**

만약 HTML 페이지에 `<form>` 요소가 존재하지 않으면, form은 null이 되고, form!는 이를 강제로 null이 아님으로 간주하여 타입스크립트의 타입 검사기를 우회한다. 이 후에 addEventListener를 호출하면 null에 대해 메서드를 호출하는 것이라 런타임 오류가 발생하게 된다.

```ts
const form = document.querySelector("form")!; // form이 null일 가능성 무시

// form이 실제로 null일 경우, 아래 코드에서 오류가 발생
form.addEventListener("submit", (event) => {
  // form이 null일 때 실행되면 오류가 발생
});
```

<br>

**[피드백]**

!을 남발하는 것은 매우 안 좋은 방식이다. !를 사용할 경우 휴먼 에러를 방지할 수 없으며, 찾고자 하는 요소가 없을 때 앱이 그냥 터져버리는 큰 문제가 발생하므로 사용을 지양해야 한다.

```ts
const form = document.querySelector('form');

if (!form) {
  // do something...
}

form.addEventListener('submit', ...)
```

<br>
<br>

## <mark class="pink">🔥생각해보기</mark>

### <mark class="yellow">1. 읽기 전용으로 만들기</mark>

**1\. Object.freeze**  
객체를 런타임에서 불변으로 만든다.

속성을 수정, 추가, 삭제할 수 없게 만든다. 컴파일 시 타입 체크가 아닌 **런타임에서 동작**한다.

```ts
const obj = Object.freeze({ name: "Alice", age: 30 });
obj.name = "Bob"; // Error: Cannot assign to read-only property
```

배열도 고정되지만 내부 요소는 참조 가능(얕은 동결)한다.

```ts
const deepObj = Object.freeze({ inner: { value: 10 } });
deepObj.inner.value = 20; // 변경 가능
```

<br>

**2\. as const**  
리터럴 객체나 배열을 **읽기 전용 리터럴 타입**으로 만든다.

컴파일 단계에서 리터럴 값을 `변하지 않는 상수 타입`으로 추론한다. 객체와 배열 모두 지원하고, 타입스크립트 수준에서 타입을 고정(불변)한다.

```ts
const obj = { name: "Alice", age: 30 } as const;
obj.name = "Bob"; // Error: Cannot assign to 'name' because it is a read-only property

const arr = [1, 2, 3] as const;
arr[0] = 4; // Error: Index signature in type 'readonly [1, 2, 3]' only permits reading
```

중첩된 구조도 고정된다. (리터럴 타입으로 추론)

```ts
const deepObj = { inner: { value: 10 } } as const;
deepObj.inner.value = 20; // Error
```

<br>

**3\. readonly**  
객체 속성을 읽기 전용으로 지정한다.

`type` 또는 `interface` 정의에서 사용한다. 객체를 런타임에서 불변으로 만들지는 않는다. 읽기 전용 속성은 컴파일러에서 변경 불가로 체크한다.

```ts
type User = {
  readonly name: string;
  age: number;
};

const user: User = { name: "Alice", age: 30 };
user.name = "Bob"; // Error: Cannot assign to 'name' because it is a read-only property
user.age = 35; // 가능
```

<br>

**4\. `Readonly<Type>`**  
기존 타입의 모든 속성을 읽기 전용으로 변환한다.

기존 타입을 쉽게 변환하는 유틸리티 타입이다. `Partial`, `Required` 등과 함께 사용 가능하다.

```ts
type User = { name: string; age: number };
const user: Readonly<User> = { name: "Alice", age: 30 };
user.age = 35; // Error: Cannot assign to 'age' because it is a read-only property
```

<br>
<br>

### <mark class="yellow">2. Type Alias vs Interface</mark>

**1\. Type Alias**  
타입에 별칭을 부여한다. 기본 타입이나 유니언 타입을 정의할 수 있다. 객체, 함수 타입도 정의 가능하다. `&` 연산자로 타입 확장을 할 수 있다.

```ts
type ID = string | number; // 유니언 타입
type Point = { x: number; y: number }; // 객체 타입
type Add = (a: number, b: number) => number; // 함수 타입

// 타입 확장
type Animal = { name: string };
type Dog = Animal & { breed: string };
```

<br>

**2\. Interface**  
객체 타입의 구조를 정의한다. `extends` 키워드로 확장할 수 있다.

```ts
// 타입 확장
interface Animal {
  name: string;
}
interface Dog extends Animal {
  breed: string;
}
```

<br>
<br>

### <mark class="yellow">3. 타입 내로잉(narrowing)</mark>

유니언 타입과 같은 넓은 타입을 더 구체적인 타입으로 좁히는 과정이다.  
타입스크립트는 코드 실행 중에 구체적인 타입을 알 수 없다. 타입 내로잉을 통해 타입을 명확히 하면 안전하게 작업할 수 있다.

1\. 유니언 타입 처리

```ts
function printId(id: string | number) {
  if (typeof id === "string") {
    console.log(id.toUpperCase()); // string으로 좁힘
  } else {
    console.log(id.toFixed(2)); // number로 좁힘
  }
}
```

2\. 타입 가드 사용

```ts
function isDog(animal: Dog | Cat): animal is Dog {
  return (animal as Dog).bark !== undefined;
}
```

3\. 속성 존재 여부 체크

```ts
function move(animal: Fish | Bird) {
  if ("swim" in animal) {
    animal.swim();
  } else {
    animal.fly();
  }
}
```

4\. `instanceof` 사용

```ts
if (date instanceof Date) {
  console.log(date.getTime()); // Date 타입으로 좁힘
}
```
