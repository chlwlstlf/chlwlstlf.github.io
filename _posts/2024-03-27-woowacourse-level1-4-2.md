---
layout: single
title: "[우테코] Level1 영화 리뷰 1단계"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션4 🎬영화 리뷰 1단계

## <mark class="pink">🔥1단계 구현 사항</mark>

[1단계 - 영화 목록 불러오기](https://github.com/woowacourse/javascript-movie-review/pull/131){: .btn .btn--primary}

### <mark class="yellow">1. Skeleton</mark>

**src/components/skeleton/Skeleton.ts**

Skeleton은 서버 또는 사용자 입력과 무관한 정적인 구조이기 때문에 XSS 공격이 낮다. 따라서 DOM API를 사용하지 않고 html 코드를 그대로 넣었다.  
한 번에 띄울 데이터가 20개이기 때문에 skeleton 또한 `repeat(20)`으로 해주었다.

```ts
const Skeleton = () => {
  const skeleton = render();
  return skeleton;
};

const render = () => {
  const skeletonHTML = `            
  <li class="skeleton-list none">
    <div class="item-card">
      <div class="item-thumbnail skeleton"></div>
      <div class="item-title skeleton"></div>
      <div class="item-score skeleton"></div>
    </div>
  </li>
  `.repeat(20);

  return skeletonHTML;
};

export default Skeleton;
```

<br>

**src/view/view.ts**

1\. `renderMovieCard`는 첫 번째 스켈레톤 바로 앞 요소에 MovieCard(`<li>`)를 삽입한다.  
2\. `showSkeleton`은 스켈레톤의 `none` 클래스를 **제거**하여 스켈레톤을 보여준다.  
3\. `hideSkeleton`은 스켈레톤의 `none` 클래스를 **추가**하여 스켈레톤을 숨긴다.

```ts
const view = {
  renderMovieCard(movies: Movie[]) {
    const skeletonList = document.querySelector(".skeleton-list");
    if (!skeletonList) return;

    movies.forEach((movie) => {
      skeletonList.before(MovieCard(movie)); // 바로 앞 요소
    });
  },

  showSkeleton() {
    const skeletons = document.querySelectorAll(".skeleton-list");
    skeletons.forEach((element) => {
      element.classList.remove("none"); // none 클래스 제거
    });
  },

  hideSkeleton() {
    const skeletons = document.querySelectorAll(".skeleton-list");
    skeletons.forEach((element) => {
      element.classList.add("none"); // none 클래스 추가
    });
  },
};

export default view;
```

<br>

**src/components/movieListWrapper/MovieListWrapper.ts**

MovieListWrapper은 title, viewType, inputValue를 인자로 받아 `.item-view`에 제목과 영화 리스트를 보여주는 클래스이다.

1\. 인자가 바뀔 때마다 MovieListWrapper 인스턴스가 새로 생성되는데 그때마다 Skeleton을 html에 삽입한다.  
2\. fetch하기 전에 `view.showSkeleton()`을 호출하여 Skeleton을 보여준다.  
3\. fetch 후 결과값을 받으면 `view.hideSkeleton()`을 호출하여 Skeleton을 숨긴다.  
4\. `view.renderMovieCard(movies)`을 호출하여 영화 리스트를 보여준다.

```ts
class MovieListWrapper {
  #currentPage: number;
  #title: string;
  #viewType: ViewType;
  #inputValue: string;

  constructor(title: string, viewType: ViewType, inputValue = "") {
    this.#currentPage = 1;
    this.#title = title;
    this.#viewType = viewType;
    this.#inputValue = inputValue;
  }

  async create() {
    const itemView = document.querySelector(".item-view");
    if (!itemView) return;

    itemView.replaceChildren();

    const title = document.createElement("h2");
    title.textContent = this.#title;

    const itemList = document.createElement("ul");
    itemList.className = "item-list";
    itemList.replaceChildren();
    itemList.innerHTML = Skeleton(); // 1. Skeleton을 html에 삽입

    const showMoreButton = document.createElement("button");
    showMoreButton.className = "show-more-btn primary full-width";
    showMoreButton.textContent = "더 보기";

    itemView.append(title, itemList, showMoreButton);

    await this.updateMovieList(showMoreButton);
    showMoreButton.addEventListener("click", async () => {
      this.#currentPage += 1;
      await this.updateMovieList(showMoreButton);
    });
  }

  private async updateMovieList(showMoreButton: HTMLButtonElement) {
    switch (this.#viewType) {
      case VIEW_TYPE.POPULAR:
        await this.updatePopularMovieList(showMoreButton);
        break;
      case VIEW_TYPE.SEARCH:
        await this.updateSearchMovieList(showMoreButton);
        break;
      default:
        break;
    }
  }

  private async updatePopularMovieList(showMoreButton: HTMLButtonElement) {
    view.showSkeleton(); // 2. Skeleton 보여줌
    const result = await fetchPopularMovieList(this.#currentPage);
    if (result) {
      view.hideSkeleton(); // 3. Skeleton 숨김
      const [movies, totalPages] = result;
      if (LAST_PAGE <= this.#currentPage) {
        showMoreButton.classList.add("none");
      }
      view.renderMovieCard(movies); // 4. 영화 리스트 보여줌
    }
  }

  private async updateSearchMovieList(showMoreButton: HTMLButtonElement) {
    view.showSkeleton();
    const result = await fetchSearchMovieList(
      this.#inputValue,
      this.#currentPage
    );
    if (result) {
      view.hideSkeleton();
      const [movies, totalPages] = result;
      if (Math.min(totalPages, LAST_PAGE) <= this.#currentPage) {
        showMoreButton.classList.add("none");
      }
      view.renderMovieCard(movies);
    }
  }
}

export default MovieListWrapper;
```

<br>

<video controls>
  <source src="https://github.com/user-attachments/assets/dc7134df-f574-49c5-bdf3-ab2b95af63d9" type="video/mp4">
</video>

<br>
<br>

### <mark class="yellow">2. loading lazy</mark>

아래 화면을 보면 loading이 완료된 이미지들이 먼저 보인다.

<video controls>
  <source src="https://github.com/user-attachments/assets/a5e198c7-f56b-4b28-b96f-e3dbb7f76c7f" type="video/mp4">
</video>

<br>

**src/components/movieCard/movieCard.ts**

1\. 썸네일 클래스에 `skeleton`을 추가한다.  
2\. `<img>` 요소에 loading="lazy" 속성을 추가하여 화면에 보일 때만 이미지를 로드하도록 설정한다. 이는 초기 페이지 로딩 속도를 개선하고, 불필요한 네트워크 요청을 줄이는 효과가 있다.  
3\. onload 이벤트: 이미지가 완전히 로드된 후 실행되는 이벤트, 이때 스켈레톤 클래스를 제거하여 실제 이미지를 자연스럽게 보여준다.

```ts
const thumbnail = document.createElement("img");
thumbnail.className = "item-thumbnail skeleton";
thumbnail.src = movie.poster_path
  ? `https://image.tmdb.org/t/p/w220_and_h330_face/${movie.poster_path}.jpg`
  : emptyPng;
thumbnail.loading = "lazy";
thumbnail.alt = movie.title;
thumbnail.onload = () => {
  thumbnail.classList.remove("skeleton");
};
```

<br>

**[결과 화면]**

이미지 로딩이 끝나면 클래스에서 `skeleton`을 제거하여 이미지를 보여준다. (개발자 도구 확인)

<video controls>
  <source src="https://github.com/user-attachments/assets/34129277-8369-4142-9b31-f28b30521dad" type="video/mp4">
</video>

<br>
<br>

## <mark class="pink">🔥1단계 피드백</mark>

### <mark class="yellow">1. 검색결과가 0개일 경우 처리 부재</mark>

**[피드백]**

검색결과가 없다는걸 사용자에게 명시적으로 알려주면 좋을 것 같다.

![Image](https://github.com/user-attachments/assets/ef2d049e-b7d4-4ce2-8e9f-d9f60cc78d29)

<br>

**[수정한 코드]**

**src/components/movieListWrapper/MovieListWrapper.ts**

movies 배열 결과가 비어있고, 첫 페이지이면 검색 결과가 없다는 것이므로 예외 처리를 하여 결과가 없을 때 화면을 보여준다.

```ts
private async updateSearchMovieList(showMoreButton: HTMLButtonElement) {
  view.showSkeleton();
  const result = await fetchSearchMovieList(this.#inputValue, this.#currentPage);
  if (result) {
    view.hideSkeleton();
    const [movies, totalPages] = result;
    if (!movies.length && totalPages == 1) { // 추가
      view.noMovieResult();
    }
    if (Math.min(totalPages, LAST_PAGE) <= this.#currentPage) {
      showMoreButton.classList.add('none');
    }
    view.renderMovieCard(movies);
  }
}
```

![Image](https://github.com/user-attachments/assets/f12964b1-a613-47a2-930d-0f9046aac415)

<br>
<br>

### <mark class="yellow">2. 네트워크 에러 시 처리 부재</mark>

**[피드백]**

1\. 의도적으로 네트워크 탭에서 네트워크를 끊어보고 더보기 버튼을 눌렀을 때 아래와 같이 처리가 되고 있다. 최소한 스켈레톤은 그만 보여져야 할 것 같다. (스켈레톤이 보이면 사용자는 계속 기다릴수도 있기 때문)  
2\. 에러문구도 개발자만 알아들을 수 있을 것 같아서 사용자에게 좀 더 친절하게 문구를 바꾸는 게 좋을 것 같다.

<video controls>
  <source src="https://github.com/user-attachments/assets/7f76d465-cc11-42ff-ad71-c1fe940dd9f4" type="video/mp4">
</video>

<br>

**[수정한 코드]**

**src/apis/fetchData.js**

window 객체를 사용하여 데이터를 요청할 때마다 offline을 감지한다.

```ts
async function fetchData(url) {
  try {
    // 오프라인 감지
    window.addEventListener("offline", () => {
      throw new Error("offline");
    });

    const response = await fetch(url);
    const data = await response.json();

    if (!response.ok) {
      throw new Error(data.status_message);
    }

    return data;
  } catch (error) {
    errorHandler(error.message);
    throw error;
  }
}
```

<br>

**src/utils/errorHandler.js**

errorHandler 함수가 호출되면 사용자의 혼돈을 야기하지 않기 위해 스켈레톤을 숨겼다.  
에러 메세지는 '어떤 오류가 떴으며, 다음에 어떻게 실행해야 할지' 를 알려주는 것이 제일 좋은 메세지인 것 같아서 이런 형태로 수정했다.

```ts
const errorHandler = (code) => {
  view.hideSkeleton();
  if (code === "Invalid API key: You must be granted a valid key.") {
    toast(
      "서비스에 접근할 수 있는 권한이 없습니다. API 키를 다시 발급 받아주세요."
    );
  } else if (code === "Failed to fetch") {
    toast(
      "이 서비스는 일시적으로 오프라인 상태입니다. 네트워크를 확인해주세요."
    );
  } else {
    toast("예기치 못 한 오류가 생겼습니다. 다시 접속해주세요.");
  }
};
```

<br>

**[결과 화면]**

<video controls>
  <source src="https://github.com/user-attachments/assets/0c9e009e-e791-444d-bf10-2da4c7b519c1" type="video/mp4">
</video>

<br>
<br>

## <mark class="pink">🔥생각해보기</mark>

### <mark class="yellow">1. 테스트</mark>

**1. 실제 API를 테스트해야 하는 경우와 fixture를 이용하여 테스트 하는 것은 어떻게 다르고 각각 언제 사용하는 것이 좋을까?**

**✅ 실제 API 테스트**  
실제 서버에 HTTP 요청을 보내고 응답을 검증하는 테스트

장점

- API와의 통신이 정상적으로 동작하는지 확인이 가능하다.
- 실제 환경에서의 데이터 흐름을 검증할 수 있다.

단점

- 네트워크 요청이 필요하기 때문에 테스트가 느려질 수 있다.
- API 변경 시 테스트가 깨질 가능성이 높다.
- 외부 서버가 다운되거나 응답이 지연되면 테스트가 실패할 수 있다.

언제 사용?

- **E2E 테스트(통합 테스트)**가 필요한 경우.
- API의 실제 응답을 검증해야 하는 경우(예: 로그인 API, 결제 API).

<br>

**✅ Fixture를 이용한 테스트**  
미리 정의된 Mock 데이터(Fixture)를 사용하는 테스트

장점

- 네트워크 요청이 필요 없기 때문에 빠르고 안정적이다.
- API 변경의 영향을 받지 않기 때문에 독립적이다.
- 테스트 환경을 쉽게 조작할 수 있다.(예: 성공/실패 케이스 테스트)

단점

- 실제 API와 다를 수 있다 → API가 변경되면 Fixture도 직접 수정해야 함
- 네트워크 이슈 등을 테스트할 수 없음(예: 응답 지연, 서버 오류).

언제 사용?

- 서버에서 API가 구현되기 전 (백엔드 인프라가 구축되지 않는 등의 이유로 실제 서버에 접근할 수 없는 환경이라면 요청할 수 없다.)
- 한정된 자원을 사용해야 하는 환경 (예: Youtube API는 일 10,000회로 제한되어 있다.)
- 실제 서버의 응답이 오래 걸릴 때 (오래 걸리는 만큼 테스트하는 시간도 그만큼 길어지기 때문이다.)

<br>
<br>

### <mark class="yellow">2. 타입스크립트</mark>

**1. 제네릭은 왜 사용할까?**

- 코드의 재사용성을 높이기 위해 사용한다.
- 특정 타입에 의존하지 않고 다양한 타입에서 동작하는 함수 또는 클래스를 만들 수 있다.
- 타입 안정성을 보장하면서 유연한 코드를 작성할 수 있다.

<br>

**2. 제네릭을 가급적 많이 사용하는 것이 좋을까? 좋다면 왜 좋고, 안 좋다면 왜 안 좋을까?**

좋은 경우

- 타입 안정성을 유지하면서 재사용 가능한 유틸리티 함수, 컴포넌트를 만들 때
- 여러 타입을 다룰 수 있어야 하는 경우(예: `Array<T>`, `Promise<T>`)

나쁜 경우

- 단순한 함수/컴포넌트에서 불필요한 제네릭 사용은 오히려 복잡도를 증가시킨다.
- 과도한 제네릭 사용은 가독성을 해칠 수 있다.
- 특정 타입만을 다룰 경우 제네릭보다 명시적인 타입을 사용하는 것이 더 직관적이다.

<br>

**3. Type과 Interface의 차이는 무엇일까? 나만의 기준 정하기**

Interface → 객체의 구조(Shape)를 정의할 때 사용  
Type → 유니온 타입, 튜플, 함수 타입 정의 등에 사용

<br>
<br>

### <mark class="yellow">3. 비동기</mark>

**1. 비동기가 무슨 뜻인가?**

코드가 순차적으로 실행되지 않고, 특정 작업이 완료될 때까지 기다리지 않는 방식이다.  
JavaScript는 기본적으로 싱글 스레드이므로 비동기 처리를 통해 블로킹을 방지하고 효율적으로 실행된다.

<br>

**2. Callback을 쓰는 데 어떤 한계가 있어서 Promise가 등장했을까?**

콜백 지옥

- 콜백 함수가 중첩되면서 코드가 복잡해지고 가독성이 떨어진다.
- 에러 핸들링이 어렵다. → try-catch 사용 불가
- 함수 분리 및 재사용이 어렵다.

<br>

**3. 비동기 함수 내에서 await 키워드를 쓰면 동기적으로 동작할까? 그렇다면, 그렇지 않다면 그 이유는?**

💡 비동기 함수 내부에서 await를 사용해도 함수 전체가 동기적으로 변하는 것은 아니다.

await는 Promise가 해결될 때까지 해당 코드의 실행을 멈추지만, 전체 코드 실행을 블로킹하지 않는다.  
await 다음 코드는 비동기적으로 실행되며, Promise의 실행이 끝날 때까지 전체 프로그램이 멈추지는 않는다.

<br>

**4. 비동기 함수에서 숫자 1을 반환한다면 어떤 타입일까?**

`Promise<number>` 타입이다. 비동기 함수(async function)는 항상 Promise를 반환하기 때문이다.  
값을 직접 얻고 싶다면 await 키워드를 사용하면 된다.
