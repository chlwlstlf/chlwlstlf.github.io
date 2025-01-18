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

### <mark class="yellow">RestaurantService</mark>

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

### <mark class="yellow">버튼 컴포넌트</mark>

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

### <mark class="yellow">insertAdjacentHTML</mark>

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
