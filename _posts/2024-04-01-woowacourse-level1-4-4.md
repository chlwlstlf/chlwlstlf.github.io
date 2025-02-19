---
layout: single
title: "[우테코] Level1 영화 리뷰 2단계"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션4 🎬영화 리뷰 2단계

## <mark class="pink">🔥2단계 구현 사항</mark>

[영화 리뷰 배포 링크](https://javascript-movie-review-red.vercel.app/){: .btn .btn--primary}

[2단계 - 상세 정보 & UI/UX 개선하기](https://github.com/woowacourse/javascript-movie-review/pull/162){: .btn .btn--primary}

### <mark class="yellow">1. apiClient</mark>

apiClient를 만들어서 API에 대한 HTTP 요청을 모듈화하였다.

1\. api_key와 language를 공통으로 사용하여 params를 사용하기 때문에 buildUrl이라는 함수를 만들었다.  
2\. method, headers, body로 options를 만든 후 fetch 함수의 두 번째 인자로 넣어준다.

```ts
const API_KEY = process.env.API_KEY;

const buildUrl = (endpoint, queryParams = {}) => {
  const params = new URLSearchParams({
    api_key: API_KEY,
    language: API.LANGUAGE,
    ...queryParams,
  });

  return `${API.URL}${endpoint}?${params}`;
};

const ApiClient = {
  get(endpoint, queryParams = {}, headers = {}) {
    const url = buildUrl(endpoint, queryParams);
    return this.request("GET", url, null, headers);
  },

  post(endpoint, body, queryParams = {}, headers = {}) {
    const url = buildUrl(endpoint, queryParams);
    return this.request("POST", url, body, headers);
  },

  put(endpoint, body, queryParams = {}, headers = {}) {
    const url = buildUrl(endpoint, queryParams);
    return this.request("PUT", url, body, headers);
  },

  delete(endpoint, queryParams = {}, headers = {}) {
    const url = buildUrl(endpoint, queryParams);
    return this.request("DELETE", url, null, headers);
  },

  async request(method, url, body = null, headers = {}) {
    const options = {
      method,
      headers: {
        "Content-Type": "application/json",
        ...headers,
      },
      body: body ? JSON.stringify(body) : null,
    };

    try {
      // 오프라인 확인
      window.addEventListener("offline", () => {
        throw new Error("offline");
      });

      const response = await fetch(url, options);
      const data = await response.json();

      if (!response.ok) {
        throw new Error(response.status);
      }

      return data;
    } catch (error) {
      errorHandler(error.message);
      throw error;
    }
  },
};

export default ApiClient;
```

<br>
<br>

### <mark class="yellow">2. 무한 스크롤</mark>

**src/utils/setupIntersectionObserver.js**

setupIntersectionObserver는 IntersectionObserver API를 설정하는 함수이다.  
즉, "특정 요소가 화면에 보이는지 감시하는 관찰자를 설정하는 함수"이다.

- 특정 요소(target)가 화면에 보이는지 감시
- 요소가 화면에 보이면 지정된 콜백 함수 실행

```js
const setupIntersectionObserver = (callback, target) => {
  const option = {
    root: null, // viewport를 root로 사용
    rootMargin: "0px", // viewport의 여백
    threshold: 0, // 요소가 1px이라도 보이면 콜백 실행
  };

  const observer = new IntersectionObserver(callback, option);
  observer.observe(target);
};

export default setupIntersectionObserver;
```

<br>

**src/components/movieListWrapper/MovieListWrapper.js**

**[동작 과정]**

1\. 페이지 하단에 end-list 요소 배치  
2\. 사용자가 스크롤하여 end-list가 뷰포트에 조금이라도 보이면
onIntersect 콜백 실행  
3\. 로딩 상태 관리로 중복 요청 방지  
4\. 새로운 영화 데이터 로드 및 렌더링  
5\. 페이지 번호 증가

```js
class MovieListWrapper {
  #currentPage;
  #isLoading;

  #title;
  #viewType;
  #inputValue;

  constructor(title, viewType, inputValue = "") {
    this.#currentPage = 1;
    this.#isLoading = false;
    this.#title = title;
    this.#viewType = viewType;
    this.#inputValue = inputValue;
  }

  async create() {
    const itemView = document.querySelector(".item-view");
    itemView.replaceChildren();

    const title = document.createElement("h2");
    title.textContent = this.#title;

    const itemList = document.createElement("ul");
    itemList.className = "item-list";
    itemList.replaceChildren();
    itemList.innerHTML = Skeleton();

    const endList = document.createElement("div");
    endList.className = "end-list"; //1. 페이지 하단에 end-list 요소 배치

    itemView.append(title, itemList, endList);

    this.infiniteScroll();
  }

  infiniteScroll() {
    const endList = document.querySelector(".end-list");

    const onIntersect = async (entries) => {
      entries.forEach(async (entry) => {
        if (entry.isIntersecting && !this.#isLoading) {
          this.#isLoading = true; // 3. 로딩 상태 관리로 중복 요청 방지
          view.showSkeleton();
          const result = await this.getMovieList();

          if (result) {
            const { movies, totalPages } = result; // 4. 새로운 영화 데이터 로드 및 렌더링
            view.hideSkeleton();
            if (this.shouldStopObserving(movies, totalPages)) {
              return;
            }
            view.renderMovieCard(movies);
            this.#isLoading = false;
            this.plusPage(); // 5. 페이지 번호 증가
          }
        }
      });
    };

    // 2. end-list가 뷰포트에 보이면 onIntersect 콜백 실행
    setupIntersectionObserver(onIntersect, endList);
  }

  // 중략
}

export default MovieListWrapper;
```

<br>

<div class="blue-box">
  <p>
    <b>💡 entries란?</b>
    <div>IntersectionObserverEntry 객체들의 배열</div>
    <br>
    <b>주요 속성들</b>
    <div>• isIntersecting: 관찰 대상이 뷰포트와 교차하는지 여부 (boolean)</div>
    <div>• intersectionRatio: 교차 비율 (0.0 ~ 1.0)</div>
    <div>• target: 관찰 대상 요소</div>
    <div>• boundingClientRect: 관찰 대상의 경계 사각형 정보</div>
    <div>• intersectionRect: 교차 영역의 경계 사각형 정보</div>
    <div>• rootBounds: 루트 요소의 경계 사각형 정보</div>
  </p>
</div>

<br>
<br>

### <mark class="yellow">3. 디바운싱</mark>

모바일 버전과 데스크탑 버전의 검색 기능이 다르게 되어야 하기 때문에 **resize 이벤트**로 header를 다르게 보여준다.  
하지만 창 크기를 변경할 때마다 이벤트가 연속적으로 실행되면 DOM 조작이나 복잡한 연산이 빈번히 일어나 브라우저 성능에 큰 영향을 줄 수 있다.

이를 방지하기 위해 debouncing을 사용하여 이벤트 실행을 최적화했다. debouncing을 사용하면 이벤트가 **마지막으로 호출**된 후 일정 시간(delay) 동안 대기한 뒤에만 실행된다.

<br>

**src/utils/eventUtils.js**

`callback`과 `delay`를 인자로 받는 debounce 함수를 만들었다.  
debounce가 마지막으로 호출된 후 `delay` 시간만큼 기다렸다가 `callback` 함수가 실행된다.

💡 이벤트가 연속적으로 발생할 경우 이전의 타이머를 취소(clearTimeout)하고 새로운 타이머를 설정하기 때문에 마지막 이벤트가 기준이다.

```ts
export const debounce = (callback, delay) => {
  let timer;

  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => {
      callback(...args);
    }, delay);
  };
};
```

<br>

**src/components/header/header.ts**

handleResize 함수는 화면 크기에 맞춰 새로운 header를 렌더링하는 함수이다.  
resize를 할 때 debounce를 걸어 비용이 큰 handleResize 함수를 화면 크기 변경이 끝난 후 0.3초 뒤에 실행하게 했다.

※ 0.3s로 지정한 이유는 UX와 성능 사이의 균형을 맞추기 위해 임의로 지정했다.  
(너무 빠르면 디바운싱 효과가 적어 성능이 안 좋아지고, 너무 느리면 화면이 멈춰있다는 느낌을 받아 UX가 안 좋아진다.)

```ts
interface Props {
  onLogoClick?: () => void;
  inputSubmitHandle?: (value: string) => void;
}

const Header = (props: Props) => {
  const render = () => {
    const isMobile = window.innerWidth <= BREAKPOINT.MOBILE;
    return isMobile ? HeaderMobile(props) : HeaderDesktop(props);
  };

  const handleResize = () => {
    const header = document.querySelector(".header");
    if (!header) return;
    header.replaceWith(render());
  };

  window.addEventListener("resize", debounce(handleResize, 300));

  return render();
};

export default Header;
```

💡 replaceWith() 방식: DOM을 완전히 교체 → input 값이 초기화됨

<br>

**[디바운싱 적용 전]**

<video controls>
  <source src="https://github.com/user-attachments/assets/4941e0bd-f91e-48e0-8ad8-ec054550257f" type="video/mp4">
</video>

<br>

**[디바운싱 적용 후]**

<video controls>
  <source src="https://github.com/user-attachments/assets/b0931cab-55ae-4cdc-8360-9c32a22d23b2" type="video/mp4">
</video>

<br>
<br>

### <mark class="yellow">4. 불필요한 이벤트 호출 줄이기</mark>

**[문제 인식]**

디바운싱으로 호출 횟수를 충분히 줄였지만 아래와 같은 고민이 더 생겼다.

1\. resize를 했을 때마다 header를 리렌더링 해야할까?  
2\. 데스크탑 -> 태블릿이면 이전과 같은 header를 보여줘도 되지 않을까?  
3\. 또 이 서비스의 모든 화면 크기는 같은데 `resize 이벤트`, `이전 viewport` 등을 한 곳에서만 관리하면 되지 않을까?

💡 resize 이벤트를 전역으로 관리하며 handleResize 호출 횟수를 더 줄여보자.

<br>

**src/utils/ViewportManager.js**

`ViewportManager`는 `구독 함수들`, `이전 viewport`, `resize 이벤트`를 전역적으로 관리하는 클래스이다.

<br>

**싱글톤 패턴**

여러 개의 인스턴스를 만들면 불필요한 resize 이벤트 리스너가 중복 실행될 가능성이 있기 때문에 애플리케이션에서 하나의 인스턴스만 유지하는 싱글톤 패턴을 사용했다. 어디에서 가져다 써도 같은 ViewportManager 인스턴스를 공유하게 한다.

<br>

**옵저버 패턴**

특정 이벤트가 발생하면, 등록된 여러 개의 "구독자(listener)"들에게 알림을 보내는 패턴이다. 뷰포트 변경을 감지하는 여러 개의 컴포넌트가 독립적으로 동작할 수 있도록 하기 위해 이 패턴을 사용했다.

`subscribe()`: 여러 컴포넌트가 뷰포트 변경을 감지할 수 있도록 하는 함수  
`notifyListeners()`: resize가 될 때 호출되는 메서드. 이 메서드가 호출될 때 모든 구독자들에게 변경을 알림

<br>

```js
class ViewportManager {
  constructor() {
    this.listeners = new Set();
    this.prevViewport = window.innerWidth;
    this.resizeHandler = this.notifyListeners.bind(this);
    window.addEventListener("resize", this.resizeHandler);
  }

  // "이전 크기 → 현재 크기" 변경을 감지하고, 필요한 경우 구독자에게 알림을 보냄
  notifyListeners() {
    const currentViewport = window.innerWidth;
    this.listeners.forEach(({ trigger, callback }) => {
      if (trigger(this.prevViewport, currentViewport)) {
        callback(currentViewport);
      }
    });

    this.prevViewport = currentViewport;
  }

  // viewport 변경을 감지하는 구독 함수
  subscribe({ trigger, callback }) {
    const listener = { trigger, callback };
    this.listeners.add(listener);

    return () => {
      this.listeners.delete(listener);
    };
  }

  // 리스너 제거 및 window 이벤트 해제
  destroy() {
    window.removeEventListener("resize", this.resizeHandler);
    this.listeners.clear();
  }
}

export default new ViewportManager(); // 하나의 인스턴스만 공유
```

<br>

✅ 아래 처럼 사용하면 구독을 해제할 수 있다.

```ts
const unsubscribe = viewportManager.subscribe({
  trigger: handleTrigger,
  callback: handleResize,
});

unsubscribe();
```

<br>

**src/components/header/header.ts**

1\. `header` 요소는 모바일과 데스크탑에서 동작이 동일하므로 이 컴포넌트에서 생성하는 것으로 코드를 변경했다.  
2\. `handleTrigger` 함수: 이전 뷰포트 타입과 현재 뷰포트 타입을 비교하여 모바일에서 다른 타입으로 변경되면 true를 반환  
3\. `handleResize` 함수: 디바운싱을 적용한 리렌더링 함수

viewportManager를 구독하며 trigger 여부와 callback 함수를 인자로 넘긴다.  
header는 항상 mount 되어 있는 상태이기 때문에 구독 해제를 할 필요가 없다.

```ts
const Header = (props: Props) => {
  const header = document.createElement("header");
  header.className = "header";

  // 헤더 클릭 시 스크롤 상단으로 이동
  header.onclick = (event) => {
    if ((event.target as HTMLElement).tagName === "HEADER") {
      window.scrollTo({ top: 0, behavior: "smooth" });
    }
  };

  const isMobile = (viewport: number) => getViewportType(viewport) === "MOBILE";

  const render = (width: number) => {
    header.innerHTML = "";
    const content = isMobile(width)
      ? HeaderMobile(props)
      : HeaderDesktop(props);
    header.appendChild(content);
  };

  const handleTrigger = (prev: number, current: number) => {
    return isMobile(prev) != isMobile(current);
  };

  const handleResize = debounce((current: number) => {
    render(current);
  }, 300);

  viewportManager.subscribe({ trigger: handleTrigger, callback: handleResize }); // viewportManager 구독

  render(window.innerWidth);

  return header;
};

export default Header;
```

<br>

**[이전-매번 호출]**

resize가 생기면 header를 리렌더링 했다.

<video controls>
  <source src="https://github.com/user-attachments/assets/7844613e-243b-46e7-9c77-6c2deef7803f" type="video/mp4">
</video>

<br>

**[현재-필요할 때만 호출]**

모바일에서 또는 모바일로 변경될 때만 header를 리렌더링 했다.  
✅ 필요할 때만 헤더가 리렌더링된다!

<video controls>
  <source src="https://github.com/user-attachments/assets/3decb2e4-f794-4136-a640-23dfd06e0e75" type="video/mp4">
</video>

<br>
<br>

### <mark class="yellow">5. 검색 기능 - 모바일 버전</mark>

**[검색 기능 - 데스크탑 버전]**

영상과 같이 검색어를 입력하고 돋보기 버튼을 누르거나 Enter 키를 누르면 검색이 된다.

<video controls>
  <source src="https://github.com/user-attachments/assets/1c177c33-e2a7-46dd-a3c3-20c7b4c59c64" type="video/mp4">
</video>

<br>

하지만 모바일 화면에서는 긴 검색창을 띄울 수가 없다. 어떻게 해야할까?

<br>

**[검색 기능 - 모바일 버전]**

1\. 기본 상태는 input 창이 닫혀있는 상태이다. (`type="button"`)  
2\. 돋보기를 누르면 input 창이 열린다. (`type="button"`)  
3\. 아무 입력도 하지 않은 상태에서 돋보기를 누르면 창이 다시 닫힌다. (`type="button"`)  
4\. input 창에 검색어를 입력하면 `submit`으로 바뀐다. (`type="submit"`)  
5\. 돋보기를 누르면 폼이 제출되면서 input 창이 닫힌다. 다음에 창을 다시 열 수 있어야 하므로 `button` 타입으로 바뀐다. (`type="button"`)

💡 검색어가 변경될 때만 버튼 타입이 `submit`으로 변경된다.  
💡 `submit` 타입일 때 `click` 이벤트가 먼저 실행되고 그 후 `submit` 이벤트가 실행된다. 따라서 `click` 이벤트 안에서 예외처리를 해주어야 한다.

```ts
const HeaderMobile = ({ onLogoClick, inputSubmitHandle }: Props) => {
  const render = () => {
    const fragment = document.createDocumentFragment();

    // 로고 생성
    const logo = document.createElement("h1");
    logo.className = "logo";
    const logoImage = document.createElement("img");
    logoImage.src = logoPng;
    logoImage.alt = "MovieList 로고";
    logo.appendChild(logoImage);

    // 검색창 생성
    const searchBox = document.createElement("form");
    searchBox.className = "search-box";

    const searchInput = document.createElement("input");
    searchInput.className = "search-input";
    searchInput.placeholder = "검색";
    searchInput.style.display = "none";

    const searchButton = document.createElement("button");
    searchButton.type = "button"; // 1. default 상태
    searchButton.className = "search-button";
    searchButton.textContent = "검색";

    searchBox.append(searchInput, searchButton);
    fragment.append(logo, searchBox);

    // <-- 중략 -->

    searchButton.addEventListener("click", () => {
      if (searchButton.type === "submit") return; // 예외처리: submit 타입이면 click 이벤트x

      const isInputVisible = searchInput.style.display === "block";

      // 3. input창이 열려있는데 입력값이 없어 button인 상태 -> 창 닫힘
      if (isInputVisible) {
        hideSearchInput();
      } else {
        showSearchInput(); // 2. 돋보기 누르면 input창이 열림
        searchInput.focus();
      }
    });

    // 4. 입력값에 따른 버튼 상태 전환
    searchInput.addEventListener("input", () => {
      const searchInputValue = searchInput.value.trim();
      toggleSearchButton(searchInputValue === "" ? "button" : "submit");
    });

    // 5. 검색 버튼 클릭 시 검색 처리
    searchBox.addEventListener("submit", (event) => {
      event.preventDefault();
      const searchInputValue = searchInput.value.trim();

      if (searchInputValue && inputSubmitHandle) {
        inputSubmitHandle(searchInputValue);
        hideSearchInput();
        toggleSearchButton("button");
      }
    });

    const showSearchInput = () => {
      searchInput.style.display = "block";
      logo.style.display = "none";
    };

    const hideSearchInput = () => {
      searchInput.style.display = "none";
      logo.style.display = "block";
    };

    const toggleSearchButton = (type: "button" | "submit" | "reset") => {
      searchButton.type = type;
    };

    return fragment;
  };

  return render();
};

export default HeaderMobile;
```

<br>

**[결과 화면]**

검색어가 입력되면 submit으로 바뀌고 그 외에는 다 button 상태인 걸 알 수 있다.

<video controls>
  <source src="https://github.com/user-attachments/assets/54206fab-c7f8-49f5-83b6-9ab99dcd7dda" type="video/mp4">
</video>

<br>
<br>

## <mark class="pink">🔥2단계 피드백</mark>

### <mark class="yellow">1. 모달 로딩 처리</mark>

**[피드백]**

현재는 모달을 열면 상세 정보 로딩이 걸려서 빈 모달을 보게되는 경우가 많은데, 개선되면 좋을 것 같다.

<video controls>
  <source src="https://github.com/user-attachments/assets/c8a9dc10-3d98-46ba-8a07-47f0153e87bc" type="video/mp4">
</video>

<br>

**[수정한 코드]**

**src/components/movieDetailModal/MovieDetailContent.ts**

1\. loading 화면을 만든다.  
2\. title과 subDetail을 none으로 초기화한다.  
3\. `thumbnail.onload`가 되면 `showModalContent` 함수를 실행시켜 모달 컨텐츠를 보이게 한다.

```ts
class MovieDetailContent {
  #movie;

  constructor(movie: DetailMovie) {
    this.#movie = movie;
  }

  render() {
    const detailContent = document.createElement("div");
    detailContent.className = "detail-content";

    const loading = this.createLoading(); //1. loading 화면 생성
    const title = this.createTitle();
    const subDetail = this.createSubDetail();

    detailContent.append(loading, title, subDetail);
    return detailContent;
  }

  createLoading() {
    const loading = document.createElement("div");
    loading.className = "detail-loading";
    return loading;
  }

  createTitle() {
    const title = document.createElement("h3");
    title.className = "detail-title";
    title.textContent = this.#movie.title;
    title.style.display = "none"; //2. title을 none으로 초기화
    return title;
  }

  createSubDetail() {
    const subDetail = document.createElement("div");
    subDetail.className = "sub-detail";

    const thumbnail = this.createThumbnail();
    const description = this.createDescription();

    subDetail.append(thumbnail, description);
    subDetail.style.display = "none"; //2. subDetail을 none으로 초기화
    return subDetail;
  }

  createThumbnail() {
    const thumbnail = document.createElement("img");
    thumbnail.className = "detail-thumbnail";
    thumbnail.src = this.#movie.posterPath
      ? imageUrl("large", this.#movie.posterPath)
      : emptyPng;
    thumbnail.alt = this.#movie.title;
    thumbnail.onload = () => {
      this.showModalContent(); //3. onload 되면 showModalContent 함수 실행
    };

    return thumbnail;
  }

  showModalContent() {
    const detailLoading = document.querySelector(
      ".detail-loading"
    ) as HTMLElement;
    const title = document.querySelector(".detail-title") as HTMLElement;
    const subDetail = document.querySelector(".sub-detail") as HTMLElement;

    if (!detailLoading || !title || !subDetail) return;

    detailLoading.style.display = "none";
    title.style.display = "flex";
    subDetail.style.display = "flex";
  }
}

export default MovieDetailContent;
```

<br>

<div class="blue-box">
  <p>
    <b>💡 CSS로 display를 제어하지 않는 이유</b>
    <div>CSS에서 display: none은 초기 숨김이 가능하지만, 로딩이 완료된 후 자동으로 보이게 하는 로직을 CSS만으로 처리하기는 어렵다.</div>
    <div>
      따라서 로딩이 끝난 onload 시점에서만 "동적으로" style을 변경하려면 JS에서 처리해야한다.
    </div>
  </p>
</div>

<br>

**[결과 화면]**

<video controls>
  <source src="https://github.com/user-attachments/assets/d35a4d35-abb8-4914-a412-61266de071f2" type="video/mp4">
</video>
