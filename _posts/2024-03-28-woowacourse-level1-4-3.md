---
layout: single
title: "[우테코] Level1 debounce, throttle, loading&Skeleton UI, lazy loading"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션4 LMS 강의 자료2

## <mark class="pink">📚debounce, throttle</mark>

[Debouncing and Throttling Explained Through Examples](https://css-tricks.com/debouncing-throttling-explained-examples/)

### <mark class="yellow">1. Debounce</mark>

보통 기계적 스위치 센서나 터치 센서 같은 걸 다룰 때 순간적으로 스위치의 접점에서 on/off가 여러 번 반복되는 현상이 있는데 이를 **바운싱 현상**이라고 한다. 스위치의 접점에서 진동 흔들림이나 짧은 순간에 붙었다 떨어졌다가 발생하는데 현실에서 자주 발생하는 일이다. 이러한 바운스 현상을 없애는 것이 **디바운싱**이라고 한다.

Debounce는 이벤트를 그룹화하여 특정 시간이 지난 후 하나의 이벤트만 발생하도록 하는 기술이다. 즉, 순차적 호출을 하나로 '그룹화'할 수 있다. 그래서 연속적으로 호출되는 함수 중 **마지막 또는 제일 처음 함수**만 호출할 수 있다.

![](https://techcourse-storage.s3.ap-northeast-2.amazonaws.com/fc6c4bb9c28d4be5a3701e775faecb13)

<br>

이 Debounce는 웹에서 주로 **AJAX 검색**에 자주 쓰인다.

예를 들어 검색창에 무언가를 검색한다고 해보자. 일반적으로는 한 글자씩 칠 때마다 API 요청을 하면서 결과값을 받아온다. 그런데 사람은 누구나 오타가 날 수 있고, 매번 요청하는 것이 효율적이지 않은 순간도 많다.

무엇보다도 이와 같은 부분은 구글맵처럼 유료 API를 사용할 때 큰 비용이 된다. 똑같은 요청을 1번만 해도 되는 걸 10번 했다고 한다면 그만큼 10배 이상의 비용 손실이 발생하는 것이다. Youtube API의 경우에도 요청 횟수가 무한이지 않기 때문에 최소화할 부분들을 고민해야 한다.

보통 사람들은 연달아 타자를 치기 때문에, 입력이 다 끝난 후에 요청을 보내도 충분한 경우가 많다. 즉 타자를 칠 때마다 타이머를 설정한 후, 특정 시간 동안 입력이 없으면 입력이 끝난 것으로 가정해보자.

💡 시간이 너무 길면 사용자 경험에 안 좋으니 적당한 시간을 정해야 한다.

<br>

**ex) 3000ms로 설정**

새로운 입력 이벤트가 발생하면 이전 타이머를 취소하여 무효화한다.  
사용자가 입력을 멈추고 3초가 지나야만 콜백 함수가 실행되게 한다.

```js
let debounce;
const $searchInput = document.querySelector("#input");
$searchInput.addEventListener("input", onSearchInputHandler);

const onSearchInputHandler = (e) => {
  if (debounce) {
    clearTimeout(debounce);
  }
  debounce = setTimeout(() => {
    console.log("ajax 요청 부분", e.target.value);
  }, 3000);
};
```

<br>
<br>

### <mark class="yellow">2. Throttle</mark>

Throttle의 사전적 의미를 목을 조르는 행위다. 이 점에서 유래하여 무언가의 출력을 조절하는 뜻도 가지고 있어, 기계장치에 연결된 액체나 기체가 흐르는 관에 달린 밸브를 조절하는 과정에서도 이 단어가 쓰인다.

출력을 조절하는 Throttle은 이벤트를 일정한 주기마다 발생하도록 하는 기술이다. 예를 들어 Throttle의 설정 시간으로 1ms를 주게 된다면, 해당 이벤트는 1ms 동안 최대 한 번만 발생하게 된다. 즉 마지막 함수가 호출된 후 일정 시간이 지나기 전에 다시 호출되지 않도록 한다.

이 Throttle은 성능 문제로 인해 **스크롤 이벤트**에서 주로 사용한다. 스크롤을 올리거나 내릴 때 이벤트가 매우 많이 발생하는데 만약 스크롤 이벤트가 발생할 때마다 많은 기능을 하는 함수가 콜백으로 할당되어있다면 UX가 굉장히 안 좋아질 수 있다. 그럴 때 Throttle을 사용해주면 된다.

<br>

**ex) 3000ms로 설정**

현재 타이머가 없을 때 새로운 타이머를 시작한다.  
타이머가 종료된 후(3초 후), throttle을 다시 null로 설정하여 다음 이벤트를 처리할 수 있도록 한다.

```js
let throttle;
const $searchInput = document.querySelector("#input");
$searchInput.addEventListener("input", onSearchInputHandler);

const onSearchInputHandler = (e) => {
  if (!throttle) {
    throttle = setTimeout(() => {
      throttle = null;
      console.log("ajax 요청 부분", e.target.value);
    }, 3000);
  }
};
```

<br>
<br>

## <mark class="pink">📚loading & Skeleton UI</mark>

### <mark class="yellow">1. Skeleton UI</mark>

스피너와 로더는 전통적으로 콘텐츠를 로드하는데 시간이 걸린다는 것을 사용자에게 알리는 방법이었다.

`Skeleton UI`는 대기 시간이 아닌 **진행 상황**에 초점을 맞추고 로딩 시간의 불만을 줄이기 때문에 기존 로더 UI를 대체하고 있다.

![](https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/f973477d-7dcc-47a8-9ab9-510a97875c49/01-skeleton-screens-react.gif)

<br>

UX적인 관점에서는 사용자는 콘텐츠가 페이지에 로드되는 동안 계속 참여해야 이탈률이 낮아진다고 본다. 단순히 대기만 하는 스피너보다는 사용자에게 **목표를 향해 나아가고 있음**을 보여줘야 했다.

유튜브나 페이스북을 보면 Skeleton UI로 대기 시간을 줄인다. 실제 콘텐츠는 동일한 속도로 로드되지만 Skeleton UI 화면은 보다 나은 사용자 경험을 제공한다.

![](https://techcourse-storage.s3.ap-northeast-2.amazonaws.com/c75a1d8813e3420a80d8da374d4eeac4)

![](https://techcourse-storage.s3.ap-northeast-2.amazonaws.com/1611aea738c148208bc5794e3b999388)

<br>

💡 여기서 중요한 것은 Skeleton은 가능한 콘텐츠와 같아야 한다는 것이다.  
너무 다른 형태의 Skeleton을 보여주면 사용자 입장에서는 당황할 수도 있다.

<br>
<br>

## <mark class="pink">📚lazy loading</mark>

### <mark class="yellow">1. lazy loading</mark>

lazy loading은 가능한 한 길게 미루어 웹페이지에서 보여줄 콘텐츠들이 실제로 화면에 보일 때까지 미룬 후 로딩하는 기술이다. 이 리소스들은 **필요할 때** 로드되어야 한다.

이미지나 동영상 같은 콘텐츠들은 페이지 성능에 가장 많이 영향을 주고 있는 요소다.

<iframe src="https://www.youtube.com/embed/CPmNHj9a0JI?start=1" 
frameborder="0" allowfullscreen></iframe>

<br>
<br>

### <mark class="yellow">2. lazy loading 사용 방법</mark>

**<mark class="yellow">Lazy loading 단계</mark>**

Lazy loading 이미지들은 두 단계로 나눌 수 있다.

1️⃣ 이미지 로딩을 사전에 막는 것  
일반적으로 `<img>` 태그는 src 속성에 이미지 URL이 설정되면 브라우저가 즉시 이미지를 로드한다.  
하지만 Lazy Loading을 위해서는 브라우저가 초기 페이지 로딩 시 불필요한 이미지 요청을 하지 않도록 해야 한다.

<br>

2️⃣ 이미지가 화면에 나타날 때 로드 트리거  
해당 이미지를 언제 로딩할 것인지 알려주어야 한다. 이를 위해서는 해당 이미지가 뷰포트에 들어왔는지 확인해야 한다.

<br>
<br>

**<mark class="yellow">1. image tag 이용 방법</mark>**

src 속성 대신 다른 속성(예: data-src)을 활용하여 브라우저가 초기에 이미지를 로드하지 않도록 설정할 수 있다. HTML이 처음 로드될 때 브라우저는 data-src를 무시하고, 이미지를 불러오지 않는다.

```html
<img data-src="https://ik.imagekit.io/demo/default-image.jpg" />
```

<br>

data-src만 지정하면, 이미지가 계속 로드되지 않기 때문에 로드 시점을 찾아 src로 변경해야 한다.

```js
const img = entry.target;
img.src = img.dataset.src;
```

<br>
<br>

**<mark class="yellow">2. 자바스크립트 이벤트를 이용한 방법</mark>**

scroll, resize 그리고 orientationChange와 같은 이벤트 리스너를 이용할 수 있다.

- scroll 이벤트: 사용자가 페이지에서 스크롤 하는 시점을 확인할 수 있다.
- resize 이벤트: 윈도우 브라우저 크기가 바뀔 때 발생한다.
- orientationChange 이벤트: 디바이스 화면이 가로 또는 세로로 바뀔 때 발생한다.

<br>
<br>

**<mark class="yellow">3. Intersection Observer API를 이용한 방법</mark>**

[Intersection Observer API](https://developer.mozilla.org/ko/docs/Web/API/Intersection_Observer_API)는 엘리먼트 요소가 뷰포트에 들어가는 것을 감지하고 액션을 취하는 것을 간단하게 할 수 있도록 도와주는 API이다.

Intersection Observer API는 이미지 로드를 지연시키기 위해 모든 이미지에 Observer를 부착시킨다. 그리고 엘리먼트가 뷰포트에 들어간 것을 감지하게 되면, `isIntersecting` 속성을 이용하여 변화를 감지하고, 이미지를 보여준다.

**[최종 코드]**

1\. 어떤 이미지가 뷰포트 안으로 들어왔는지 확인한다.  
2\. 뷰포트 안으로 들어가면 태그의 data-src 속성에 지정된 URL을 src 속성에 넣어서 이미지를 로드한다.  
3\. 이후 나중에 트리거 이벤트를 일으키기 위해 로딩을 지연시킬 이미지로 식별하던 lazy 클래스를 제거한다.  
4\. observer 관찰을 중지한다.

```html
<img
  class="lazy"
  data-src="https://ik.imagekit.io/demo/default-image.jpg"
  alt="Lazy Loaded Image"
/>
```

```js
document.addEventListener("DOMContentLoaded", function () {
  const lazyImages = document.querySelectorAll("img.lazy");

  const observer = new IntersectionObserver((entries, observer) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        const img = entry.target;
        img.src = img.dataset.src; // 2. data-src 값을 src로 변경
        img.classList.remove("lazy"); // 3. 더 이상 lazy 상태가 아님
        observer.unobserve(img); // 4. 관찰 중지
      }
    });
  });

  lazyImages.forEach((img) => observer.observe(img));
});
```

<br>
<br>

**<mark class="yellow">4. Native lazy loading</mark>**

PC버전 크롬 기준 2019년 이후 브라우저에서는 임베딩할 이미지 또는 iframe에 'loading' 속성만 추가하는 방식으로 Native lazy loading을 이용할 수 있다.

```html
<img src="image.jpg" alt="..." loading="lazy" />
```

```html
<iframe src="video-player.html" title="..." loading="lazy"></iframe>
```

<div class="blue-box">
  <b>💡 loading 속성</b>
  <div>• lazy: 뷰포트에서 일정한 거리에 닿을 때까지 로딩을 지연 시킨다.</div>
  <div>• eager: 현재 페이지 위치가 위, 아래 어디에 위치하든 상관없이, 페이지가 로딩되자마자 해당 요소를 로딩한다.</div>
</div>
