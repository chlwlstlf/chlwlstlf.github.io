---
layout: single
title: "[Next.js] 좌석 배치도 만들기"
categories: next
toc: true
toc_sticky: true
---

# CON:SEAT의 꽃은 🌸좌석 배치도🌸

<br>

**관련 PR**  
[좌석 클릭 기능 추가](https://github.com/ConSeat/frontend/pull/55)  
[모바일 좌석 선택 scale 오류 해결](https://github.com/ConSeat/frontend/pull/71)  
[시야 찾기 페이지 api 연결](https://github.com/ConSeat/frontend/pull/91)  
[모바일 좌석 배치도 색상 밝게 안 되는 이슈 해결](https://github.com/ConSeat/frontend/pull/129)

<br>

## <mark class="pink">🔥1. 클릭 요소 정하기</mark>

**<mark class="yellow">1. svg 요소에 `btn` 추가</mark>**

- stage나 층수 정보는 클릭이 되면 안 되고, 좌석은 클릭이 되어야 한다.
- 이를 구분하기 위해 디자이너에게 클릭 가능한 요소들만 id에 `btn_` prefix를 추가해달라고 부탁했다.

<br>

**<mark class="yellow">2. svg 요소에 `sectionId` 추가</mark>**

- 그렇게 클릭을 할 수 있게 만들었다. 클릭한 요소의 id를 출력해보니 `btn_floorA`라고 떴다.
- 백엔드에 보내야하는 `sectionId` 정보가 없어 다시 백엔드 팀원과 디자이너에게 `sectionId`까지 포함한 id를 만들어달라고 했다.

<br>

![Image](https://github.com/user-attachments/assets/82376aeb-4b94-4e38-b9f0-84c163a67a4e)

(감사합니다😭👏)

<br>

**<mark class="yellow">[최종 SVG]</mark>**

`btn`과 `sectionId`가 모두 생겼다.

![Image](https://github.com/user-attachments/assets/0c15623a-5a62-4793-abac-5f5197f19efd)

<br>
<br>

## <mark class="pink">🔥2. SVG fetching해서 보여주기</mark>

이렇게 완성된 SVG를 S3+CloudFront에 저장했다. 프론트가 SVG 파일을 갖고 있으면 앞으로 경기장 개수가 늘어날수록 번들 크기와 로딩 비용이 그만큼 커지기 때문이다.

<br>

**<mark class="yellow">Image로 SVG 보여주기</mark>**

CloudFront url로 좌석배치도 SVG를 가져와서 Image로 보여준다.

**getAssetUrl.ts**

```ts
export const getStadiumAssetUrl = (stadiumId: number) =>
  `${PUBLIC_ENV.assetsUrl}/stadium/${stadiumId}.svg`;
```

**DetailPage.tsx**

```tsx
<Image src={getStadiumAssetUrl(stadiumId)} width={316} height={291} alt="" />
```

<br>

**<mark class="yellow">🤔문제점</mark>**

- 하지만 여기에는 css를 추가하거나 클릭하거나 classList를 조작할 수 없다.
- 그 이유는 img의 src로 SVG를 삽입하면 그걸 "이미지 비트맵"으로만 처리해서 내부 `<g>` 요소가 DOM에 드러나지 않기 때문이다.

<br>

**<mark class="yellow">🌈해결방법</mark>**

- svg를 다시 fetching해서 보여주는 방식으로 SVG를 인라인화하면 된다.
- 가져온 SVG 문자열을 DOM에 직접 심어주어 해결할 수 있다.

<br>

**<mark class="yellow">fetching 코드</mark>**

1\. `fetchStageSvg` 라는 fetching하는 함수를 만든다.

2\. `svgCache`는 완성된 SVG 문자열을 저장하고, `svgRequestCache`는 진행 중인 fetch 요청을 저장한다. 아직 받아오고 있는 중인 응답도 캐싱해두면 fetch -> 처리 -> 캐싱의 흐름이 한 번만 일어나고 이후엔 즉시 결과나 진행 중인 Promise를 재사용할 수 있어서 훨씬 효율적이다.

3\. fetching을 실행한다. svgCache에 있으면 바로 return하고, 아니면 fetching한다. fetching 결과도 svgCache에 캐싱한다.

4\. `dangerouslySetInnerHTML`로 SVG 문자열을 DOM에 넣어준다. React의 가상돔 관리 하에서는 JSX로 표현되지 않는 직접적인 DOM 변경을 허용하지 않는다. `dangerouslySetInnerHTML`은 그 유일한 예외로 이 부분만 React의 관리 밖으로 빼고 그대로 넣을 수 있다.

```tsx
// 2. svgCache, svgRequestCache 캐싱
const svgCache: Record<number, string> = {};
const svgRequestCache: Record<number, Promise<string>> = {};

const StageView = ({
  stadiumId,
  selectedSectionId,
  onSelectSection,
}: StageViewProps) => {
  const containerRef = useRef<HTMLDivElement>(null);
  const wrapperRef = useRef<HTMLDivElement>(null);
  const minimapRef = useRef<HTMLDivElement>(null);
  const [innerHTML, setInnerHTML] = useState<string | undefined>(
    svgCache[stadiumId]
  );

  // 3. stadiumId 변경 시 SVG fetch 또는 캐시 활용
  useEffect(() => {
    // 캐시에 있으면 바로 사용
    if (svgCache[stadiumId]) {
      setInnerHTML(svgCache[stadiumId]);
      return;
    }

    let ignore = false;
    const fetchSvg = async () => {
      if (!svgRequestCache[stadiumId]) {
        svgRequestCache[stadiumId] = fetchStageSvg(stadiumId);
      }

      try {
        const svg = await svgRequestCache[stadiumId];
        if (ignore) return;

        svgCache[stadiumId] = svg;
        setInnerHTML(svg);
      } catch (err) {
        console.error("Error fetching SVG:", stadiumId, svgUrl, err);
      }
    };

    fetchSvg();

    return () => {
      ignore = true;
    };
  }, [stadiumId]);

  return (
    <>
      <MiniMap
        stageSVGSrc={svgUrl}
        minimapRef={minimapRef}
        containerAspectRatio={containerAspectRatio}
        viewportBox={viewportBox}
      />
      <div className={styles.container} ref={containerRef}>
        <div
          ref={wrapperRef}
          className={styles.imageWrapper}
          onClick={handleSVGClick}
          // 4. SVG 문자열 삽입
          dangerouslySetInnerHTML={useMemo(
            () => (innerHTML ? { __html: innerHTML } : undefined),
            [innerHTML]
          )}
        />
      </div>
    </>
  );
};

export default StageView;

// 1. svg fetching 함수 만들기
async function fetchStageSvg(id: number) {
  const response = await fetch(getStadiumAssetUrl(id));
  return response.text();
}
```

<br>

<div class="blue-box">
  <b>※ useMemo 사용한 이유</b>
  <br>
  <div>{ __html: innerHTML } 객체를 렌더링마다 새로 생성하지 않도록 하기 위해서다.</div>
  <div>줌/팬 이벤트가 일어날 때마다 React가 전체 div를 리렌더링하는데 이때 새로운 DOM을 다시 덮어쓴다.</div>
  <div>이러면 사용자가 클릭하여 변경한 사항들이 초기화된다.</div>
  <br>
  <div>useMemo로 묶어서 innerHTML이 실제로 바뀔 때만 새 객체를 만들고, 렌더링에서는 이전 객체(SVG)를 재사용한다.</div>
</div>

<br>
<br>

## <mark class="pink">🔥3. 좌석 선택하기</mark>

fetching을 했으니 이제 좌석을 선택하고 거기서 `sectionId`를 추출해야 한다.  
좌석 선택 전후 UI는 아래와 같다.

<br>

|                                      선택 안 했을 때                                      |                                       선택 했을 때                                        |
| :---------------------------------------------------------------------------------------: | :---------------------------------------------------------------------------------------: |
|                                    원래 색으로 보인다                                     |                     모든 좌석은 어두워지고<br>선택한 좌석만 밝아진다                      |
| ![image](https://github.com/user-attachments/assets/c3ee6cf1-c7cd-4e80-919d-c8bcdbe94f80) | ![image](https://github.com/user-attachments/assets/803e932a-77d1-4989-a6d1-276ac2fe872e) |

<br>

**StageView.tsx**

- `target`은 내가 누른 element이다. 이 target과 가장 가까운 `g[id^="btn"]`를 찾아 `group`에 저장한다.
- `svg`는 좌석배치도 전체를 의미한다.
- 좌석 하나를 클릭하면 모든 g 태그의 `.selected`를 제거하여 초기화한다.
- 모든 좌석은 어두워져야 하므로 svg에 `.gHasSelection` 클래스를 추가한다. 그리고 클릭한 id의 g태그를 찾아 `.selected` 클래스를 추가한다.
- `group.id`는 `btn_2F_43_40`이고 `parseBtnId` 함수를 사용하여 sectionId인 40과 sectionName인 "2F_43"을 추출한다.

```tsx
const handleSVGClick = (e: React.MouseEvent<HTMLDivElement>) => {
  const target = e.target as Element;
  const group = target.closest('g[id^="btn"]');
  if (!group) return;

  const svg = wrapperRef.current?.querySelector("svg");
  if (!svg) return;

  // 모두 해제
  svg
    .querySelectorAll('g[id^="btn"]')
    .forEach((g) => g.classList.remove(styles.selected));

  // 클릭된 것만 selected 추가
  svg.classList.add(styles.gHasSelection);
  svg.querySelector(`g[id="${group.id}"]`)!.classList.add(styles.selected);

  const { sectionId, sectionName } = parseBtnId(group.id);
  if (!sectionId) return;

  onSelectSection({ sectionId, sectionName });
};
```

<br>

**StageView.module.scss**

```scss
svg g[id^="btn"] {
  cursor: pointer;
  filter: none;
}

// 선택된 좌석 밝게
svg g[id^="btn"].selected > path {
  opacity: 1;
  filter: brightness(2) drop-shadow(10px 10px 20px rgb(0 0 0 / 70%));
}

// 선택 안 된 모든 좌석 어둡게
svg.gHasSelection g[id^="btn"]:not(.selected) > path {
  opacity: 0.5;
  transition: opacity 0.2s ease;
}
```

<br>
<br>

**<mark class="yellow">결과 화면</mark>**

좌석을 선택하면 svg 태그에 `.gHasSelection` 클래스가 추가되고, 선택한 g 태그에도 `.selected` 클래스가 추가된다.

![image](https://github.com/user-attachments/assets/ff5b8b86-b884-41bb-9598-3c7ed6285974)

<br>
<br>

## <mark class="pink">🔨4. SVG를 화면 밖으로 내보낼 수 있다?</mark>

**<mark class="yellow">🤔문제점</mark>**

이미지가 이동할 수 있는 최대 범위를 정해놓지 않아 화면 밖으로 SVG가 나갈 수 있는 문제가 생겼다.

<br>

**<mark class="yellow">🌈해결방법</mark>**

scale에 따른 최대 이동 범위를 제한한다.

<br>

**<mark class="yellow">1. clamp 유틸 함수 만들기</mark>**

- clamp는 주어진 값을 최소(min)와 최대(max) 범위 안으로 `강제 제한`하는 함수이다.
- min보다 value가 작으면 min을, max보다 value가 크면 max를 반환한다.

**clamp.ts**

```ts
export const clamp = (value: number, min: number, max: number) => {
  return Math.min(Math.max(value, min), max);
};
```

<br>

**<mark class="yellow">2. getTranslateLimits로 이동 가능 범위 계산하기</mark>**

- `wrapper.offsetWidth`에 `scale`을 곱해서 확대 후의 전체 가로 길이를 구한다.
- `container.getBoundingClientRect().width`는 container 요소의 현재 화면에 보이는 가로 폭이다.
- 확대된 이미지 너비에서 화면의 너비를 빼면 이미지가 화면을 얼마만큼 넘치고 있는지 나온다.
- 이 넘치는 부분을 양끝으로 반씩 나눈 값을 이동할 수 있는 최대 거리로 정했다.

ex) 확대 후 wrapper의 width가 800이고 container의 width는 400일 때 minX는 -200, maxX는 200이다.

**useStageTransform.ts**

```ts
const getTranslateLimits = () => {
  const container = containerRef.current;
  const wrapper = wrapperRef.current;
  const scale = dragState.current.scale;

  if (!container || !wrapper) {
    return { minX: 0, maxX: 0, minY: 0, maxY: 0 };
  }

  // scaled wrapper 크기
  const wrapperWidth = wrapper.offsetWidth * scale;
  const wrapperHeight = wrapper.offsetHeight * scale;
  const containerRect = container.getBoundingClientRect();

  // 화면 중앙 기준으로 벗어날 수 있는 최대 거리
  const xCenterFix = (wrapperWidth - containerRect.width) / 2;
  const yCenterFix = (wrapperHeight - containerRect.height) / 2;

  const minX = -xCenterFix;
  const maxX = xCenterFix;

  const minY = -yCenterFix;
  const maxY = yCenterFix;

  return { minX, maxX, minY, maxY };
};
```

<br>

**<mark class="yellow">3. updateTransform로 제한하기</mark>**

**useStageTransform.ts**

위 예시에서 minX는 -200, maxX는 200였다.  
사용자가 드래그해서 `translateX = 400`이 되었다면 clam(400, -200, 200)은 200,  
`translateX = -300`였다면 clam(-300, -200, 200)은 -200이 되어  
이미지는 절대로 container 바깥으로 200px 이상 밀려 나가지 않게 된다.

```ts
const updateTransform = () => {
  if (!wrapperRef.current || !containerRef.current) return;

  // 1. limits 계산
  const limits = getTranslateLimits();

  // 2. 현재 확대 상태 가져오기
  let { translateX, translateY } = dragState.current;

  // 3. clamp로 화면 밖으로 나가지 않도록 제한
  translateX = clamp(translateX, limits.minX, limits.maxX);
  translateY = clamp(translateY, limits.minY, limits.maxY);

  // 4. 상태 업데이트
  dragState.current.translateX = translateX;
  dragState.current.translateY = translateY;

  // 5. CSS transform 적용
  wrapperRef.current.style.transform = `translate(${translateX}px, ${translateY}px) scale(${dragState.current.scale})`;

  updateViewportBox();
};
```

<br>

**[translateX가 0일 때]**

그림이 한 가운데 있을 때가 0이다. 이때 더 왼쪽을 보려고 하면 translateX 값이 +가 되고, 그림의 오른쪽을 보려고 하면 값이 -가 된다. 이 translateX의 최소최대 범위를 조정하여 사진이 밖으로 나가지 못 하게 했다.

![image](https://github.com/user-attachments/assets/b45ddc34-5667-44d4-8059-e5a5954e8f6a)

<br>

**[오른쪽으로 이동시킨 경우]**

scale이 2일 때 container.width가 400px, wrapper.width가 800px이 된다.
이때 minX는 -200, maxX는 200이 되며 왼쪽 시작점을 맞추면 translateX가 200px이 된다. 브라우저 입장에서는 사진이 오른쪽으로 200px 이동한 것이기 때문이다.

![image](https://github.com/user-attachments/assets/f5df2b06-d8b3-473f-91b4-7c112ad8eb73)

<br>
<br>

## <mark class="pink">🔥5. 미니맵 보여주기</mark>

**<mark class="yellow">1. container와 비율 맞추기</mark>**

미니맵은 최대한 싱크를 맞추기 위해 container와 비율을 같게 하였다.

|                                         데스크탑                                          |                                          모바일                                           |
| :---------------------------------------------------------------------------------------: | :---------------------------------------------------------------------------------------: |
| ![image](https://github.com/user-attachments/assets/7cb0eae7-b63d-40bb-9ad3-005b001a32df) | ![image](https://github.com/user-attachments/assets/db5f7122-c676-4dd4-b368-dcda3ce2a2b6) |

<br>

**MiniMap.tsx**

`containerAspectRatio`를 인자로 받아 div의 `aspectRatio`에 담고 container의 25%로 맞췄다.  
디자인&기획에 확대되지 않았다면 MiniMap을 띄우지 않는 것으로 되어 있어서 scale이 1이면 minimap을 안 보이게 했다.

{% raw %}

```tsx
<div
  className={styles.minimap}
  ref={minimapRef}
  style={{
    width: "25%",
    aspectRatio: containerAspectRatio.toFixed(5),
    visibility: viewportBox.scale === 1 ? "hidden" : "visible",
  }}
>
  {/* 좌석 배치도 SVG */}
  {/* 빨간 네모 박스 */}
</div>
```

{% endraw %}

<br>

**useStageTransform.ts**

`containerAspectRatio`는 훅 안의 `updateViewportBox` 함수에서 계산한 것으로 `(containerWidth / containerHeight)` 즉, container의 가로와 세로 비율을 말한다.

```ts
const updateViewportBox = () => {
  const container = containerRef.current;
  const wrapper = wrapperRef.current;
  const minimap = minimapRef.current;
  if (!container || !wrapper || !minimap) return;

  const { scale, translateX, translateY } = dragState.current;

  // 컨테이너 크기 측정
  const containerWidth = container.offsetWidth;
  const containerHeight = container.offsetHeight;

  // 컨테이너 비율 저장 (미니맵의 aspectRatio 용)
  if (containerHeight !== 0) {
    setContainerAspectRatio(containerWidth / containerHeight);
  }

  const imageWidth = wrapper.offsetWidth;
  const imageHeight = wrapper.offsetHeight;

  const minimapWidth = minimap.offsetWidth;
  const minimapHeight = minimap.offsetHeight;

  const visibleWidth = containerWidth / scale;
  const visibleHeight = containerHeight / scale;

  const offsetX = (imageWidth - visibleWidth) / 2 - translateX / scale;
  const offsetY = (imageHeight - visibleHeight) / 2 - translateY / scale;

  const scaleX = minimapWidth / imageWidth;
  const scaleY = minimapHeight / imageHeight;

  setViewportBox({
    scale,
    width: visibleWidth * scaleX,
    height: visibleHeight * scaleY,
    left: offsetX * scaleX,
    top: offsetY * scaleY,
  });
};
```

<br>
<br>

**<mark class="yellow">2. 빨간 네모 박스 보여주기</mark>**

1. 현재 확대(scale)와 드래그 이동량(translateX/Y) 을 읽어 온다.

2. container, wrapper, minimap의 가로 세로 길이를 계산한다.

3. 화면에 보이는 영역(visible) 크기 계산  
   드래그·확대된 상태(scale)를 고려해 "실제 이미지 좌표계"에서 보이는 크기를 구한다.  
   ex) 화면 위에 200px로 그려진 것이, 원본에선 100px 이므로 나눠 준다.

4. 보이는 영역의 원본 이미지 내 오프셋(offset) 계산  
   `(imageSize - visibleSize)/2` 로 중앙 정렬 오프셋을 구하고, `- translate/scale` 만큼 이동된 만큼 빼 주면, “이미지 좌표계 기준 보이는 영역의 왼쪽·위쪽 위치”가 나온다.

5. 원본 이미지 -> 미니맵 스케일 비율 계산  
   `minimapWidth / wrapperWidth` : 원본 이미지 1px이 미니맵에서 px인지  
   `minimapHeight / wrapperHeight` : 세로도 동일

<br>

**useStageTransform.ts**

```ts
const updateViewportBox = () => {
  const container = containerRef.current;
  const wrapper = wrapperRef.current;
  const minimap = minimapRef.current;
  if (!container || !wrapper || !minimap) return;

  // 1. 현재 확대·이동 상태 가져오기
  const { scale, translateX, translateY } = dragState.current;

  // 2-1. 컨테이너 크기 측정
  const containerWidth = container.offsetWidth;
  const containerHeight = container.offsetHeight;

  if (containerHeight !== 0) {
    setContainerAspectRatio(containerWidth / containerHeight);
  }

  // 2-2. wrapper의 크기 측정
  const wrapperWidth = wrapper.offsetWidth;
  const wrapperHeight = wrapper.offsetHeight;

  // 2-3. 미니맵 박스 크기 측정
  const minimapWidth = minimap.offsetWidth;
  const minimapHeight = minimap.offsetHeight;

  // 3. 화면에 보이는 영역(visible) 크기 계산
  const visibleWidth = containerWidth / scale;
  const visibleHeight = containerHeight / scale;

  // 4. 보이는 영역의 원본 이미지 내 오프셋(offset) 계산
  const offsetX = (wrapperWidth - visibleWidth) / 2 - translateX / scale;
  const offsetY = (wrapperHeight - visibleHeight) / 2 - translateY / scale;

  // 5. 원본 이미지 → 미니맵 스케일 비율 계산
  const scaleX = minimapWidth / wrapperWidth;
  const scaleY = minimapHeight / wrapperHeight;

  setViewportBox({
    scale,
    width: visibleWidth * scaleX,
    height: visibleHeight * scaleY,
    left: offsetX * scaleX,
    top: offsetY * scaleY,
  });
};
```

<br>
<br>

**<mark class="yellow">결과 화면: scale 2일 때</mark>**

![image](https://github.com/user-attachments/assets/a64047d8-65a6-46bf-bc4e-5c77a5212f12)

<video controls>
  <source src="https://github.com/user-attachments/assets/cd2fa9c2-b98f-4a6d-b4fa-470488818628" type="video/mp4">
</video>

<br>
<br>

## <mark class="pink">🔨6. 이전으로 가면 선택한 좌석이 풀린다?</mark>

**<mark class="yellow">🤔문제점</mark>**

퍼널에서 열 선택에서 이전으로 가면 좌석 배치도가 보이는데 선택했던 구역이 풀려 안 보였다.

<br>

**<mark class="yellow">💡원인</mark>**

퍼널 데이터에 선택한 구역은 저장되어 있었지만 좌석 배치도가 다시 마운트되면서 추가했던 클래스가 사라졌다.

선택한 구역이 있다면 이를 좌석 배치도에 다시 보여주는 작업을 해야한다.

<br>

**<mark class="yellow">🌈해결방법</mark>**

**StageView.tsx**

`selectedSectionId`는 퍼널 데이터의 `sectionId`이다.  
svg 안에 있는 `g[id^="btn"]` 요소를 모두 탐색하며 해당 `sectionId`에 `.selected` 클래스를 주입한다.  
이때 svg에도 `.gHasSelection` 클래스를 주입하여 어둡게 만든다.

```tsx
// 페이지 복귀 시, 기존 선택 상태 복원
useEffect(() => {
  if (selectedSectionId === null || !wrapperRef.current) return;

  const svg = wrapperRef.current.querySelector("svg");
  if (!svg) return;

  svg.querySelectorAll('g[id^="btn"]').forEach((g) => {
    g.classList.remove(styles.selected);

    const { sectionId } = parseBtnId(g.id);
    if (sectionId === selectedSectionId) {
      g.classList.add(styles.selected);
      svg.classList.add(styles.gHasSelection);
    }
  });
}, []);
```

<br>
<br>

## <mark class="pink">🔨7. 모바일에서는 좌석이 안 밝아진다?</mark>

**<mark class="yellow">🤔문제점</mark>**

아이폰+크롬 환경에서 `filter: brightness()`가 적용되지 않는 오류가 발생했다.

```scss
svg g[id^="btn"].selected > path {
  opacity: 1;
  filter: brightness(2) drop-shadow(10px 10px 20px rgb(0 0 0 / 70%));
}
```

| 데스크탑(윈도우, 크롬)                                                                                                     | 모바일(아이폰, 크롬)                                                                                                       |
| -------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| <img src="https://github.com/user-attachments/assets/2ff2986c-53a0-4b45-9652-4174bfac9d5f" alt="image (30)" width="400" /> | <img src="https://github.com/user-attachments/assets/c7ce9c8b-fab1-4e2f-8d96-a78a6c13e7fe" alt="image (29)" width="400" /> |

<br>

**<mark class="yellow">💡원인</mark>**

참고: [https://stackoverflow.com/questions/58132952/how-to-set-brightness-over-a-svg-path-element](https://stackoverflow.com/questions/58132952/how-to-set-brightness-over-a-svg-path-element)

- HTML 요소: 렌더링 → 비트맵 생성 → CSS filter 적용
- SVG 벡터 요소: Blink/WebKit에서는 비트맵 생성 과정을 건너뛰어, CSS filter가 제대로 적용되지 않는 오류였다.

<br>

**<mark class="yellow">🌈해결방법</mark>**

CSS filter는 비트맵 레이어에서 적용되는데 SVG는 비트맵 변환 단계없이 직접 벡터를 그린다.  
그래서 SVG에 CSS filter와 동일한 형태를 가진 요소를 정의하고 `filter: url()`로 그 요소를 참조하여 같은 효과를 줄 수 있다.

<br>

**1\. `<filter>` 요소 생성**

- SVG 문자열에 밝기 2.5배 - `brightness(2.5)`와 동일
- DropShadow 삽입 - `drop-shadow(10px 10px 20px rgb(0 0 0 / 70%))`와 동일

**StageView.tsx**

```ts
const injectFilter = (raw: string): string => {
  const filterDefs = `
    <defs> // store graphical objects
      <filter id="bright">
        <feComponentTransfer>
          <feFuncR type="linear" slope="2.5"/>
          <feFuncG type="linear" slope="2.5"/>
          <feFuncB type="linear" slope="2.5"/>
        </feComponentTransfer>
        <feDropShadow dx="10" dy="10" stdDeviation="20" flood-color="rgba(0,0,0,0.7)"/>
      </filter>
    </defs>`;
  return raw.replace(/<svg([^>]*)>/, `<svg$1>${filterDefs}`); // svg 바로 뒤를 찾아 filterDefs를 붙여줌
};
```

<br>

**2\. fetching할 때 `<defs>` 주입**

fetching 결과에 `<defs>`를 주입한 후 캐싱한다.

**StageView.tsx**

```tsx
const fetchSvg = async () => {
  if (!svgRequestCache[stadiumId]) {
    svgRequestCache[stadiumId] = fetchStageSvg(stadiumId);
  }

  try {
    const raw = await svgRequestCache[stadiumId];
    if (ignore) return;

    const svgWithDefs = injectFilter(raw); // <defs> 주입한 후 캐싱한다
    svgCache[stadiumId] = svgWithDefs;
    setInnerHTML(svgWithDefs);
  } catch (err) {
    console.error("Error fetching SVG:", stadiumId, svgUrl, err);
  }
};
```

<br>

**3\. css에 적용**

url로 filter의 id를 참조한다.

**StageView.module.scss**

```scss
svg g[id^="btn"].selected > path {
  opacity: 1;
  filter: url("#bright");
}
```

![image](https://github.com/user-attachments/assets/2af545d7-4d1b-418b-a2f5-6eaa47d2ad47)

<br>

**결과 화면**

| 데스크탑(크롬)                                                                                                             | 모바일(아이폰, 크롬)                                                                                                       | 모바일(아이폰, 사파리)                                                                                                     |
| -------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| <img src="https://github.com/user-attachments/assets/ace97dc7-c689-4e8a-aa26-0852beb2e76f" alt="image (31)" width="400" /> | <img src="https://github.com/user-attachments/assets/f48aea24-52b1-48e3-8b61-beb24d5a931d" alt="image (33)" width="400" /> | <img src="https://github.com/user-attachments/assets/5872fcc8-7429-44d6-af3b-5392a28872de" alt="image (32)" width="400" /> |
