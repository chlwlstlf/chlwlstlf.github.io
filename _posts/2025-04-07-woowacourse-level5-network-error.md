---
layout: single
title: "[우테코] Level5 네트워크 에러 잡기"
categories: woowacourse
toc: true
toc_sticky: true
---

# 오프라인 상태입니다. 네트워크를 확인해주세요.

관련 PR: [Errorboundary 전략 세우기, Network에 따른 UI 변경](https://github.com/woowacourse-teams/2024-corea/pull/814)

## <mark class="pink">🔥1. Toast 여러 개 띄우기</mark>

**<mark class="yellow">네트워크 오류일 때는 토스트 계속 띄우기</mark>**

저희 서비스는 오프라인일 때 이용할 수 있는 서비스가 없기 때문에 사용자의 빠른 네트워크 연결을 유도해야 합니다.

이러한 이유로 아래와 같이 리팩토링을 하였습니다.

관련 커밋: [feat: toast 여러개 띄우는 것으로 변경](https://github.com/woowacourse-teams/2024-corea/pull/814/commits/b1855c8efefd4b5c27eba8d85e5751d725be8717)

<br>
<br>

**<mark class="yellow">1\. showToast 함수에 type 추가</mark>**

**[이전]**

- useToast 훅을 선언할 때 type(`error`|`success`)을 prop으로 넘겨 주었습니다.
- 하나의 컴포넌트 안에서 네트워크 상태에 따라 '오프라인 시 에러', 온라인 시 성공' 토스트를 모두 써야 하는데, 훅 생성 시점에 한 가지만 고정하도록 되어 있었습니다.

```tsx
const { showToast } = useToast("error");
```

<br>

**[이후]**

- 훅 선언부가 아니라 showToast 함수를 호출할 때마다 type을 인자로 넘기도록 옮겼습니다.
- 이렇게 하면 하나의 컴포넌트 안에서도 상황별로 원하는 타입의 토스트를 모두 띄울 수 있습니다.

```tsx
const { showToast } = useToast();

showToast("다시 시도해주세요.", "error");
showToast("성공하였습니다.", "success");
```

<br>
<br>

**<mark class="yellow">2\. showToast 함수에 durationMs 추가</mark>**

**[이전]**

- `durationMs`가 없고 항상 2500ms를 보여주었습니다.

```ts
const showToast = (message: string, type: ToastType = "error") => {
  setToast({ isOpen: true, message, type });

  setTimeout(() => {
    setToast((prev) => ({ ...prev, isOpen: false, type }));
  }, 2500);
};
```

<br>

**[이후]**

- 무한으로 보여주는 Toast가 필요하여 `showToast` 함수에 `durationMs` 인자를 추가하였고, 무한으로 띄우고 싶을 땐 `Infinity`를 사용하였습니다. (`Infinity`도 숫자 타입)
- `durationMs`가 끝났을 때 자동으로 닫히는 건 아래에 `<Toast />`에서 설명하겠습니다.

useToast.ts

```ts
// durationMs 기본은 2500이며 무한으로 띄우고 싶을 땐 Infinity 사용
const showToast = (
  message: string,
  type: ToastType = "error",
  durationMs = 2500
) => {
  setToasts((prev) => {
    return [...prev, { message, type, durationMs }];
  });
};
```

<br>
<br>

**<mark class="yellow">3\. useToast 훅에 closeToast 함수 추가</mark>**

- 무한으로 보여주는 Toast가 생겼기 때문에 닫는 동작도 추가하였습니다.
- 현재 떠있는 toast 중에서 props의 message와 동일 toast를 닫습니다.

useToast.ts

```ts
const closeToast = (message: string) => {
  setToasts((prev) => prev.filter((t) => t.message !== message));
};
```

useNetwork.ts

```ts
showToast(MESSAGES.ERROR.OFFLINE, "error", Infinity); // 오프라인 에러 토스트 열기
closeToast(MESSAGES.ERROR.OFFLINE); // 오프라인 에러 토스트 닫기
```

<br>
<br>

**<mark class="yellow">4\. showToast 함수에 message로 중복 처리</mark>**

- 사용자들이 같은 버튼을 계속 누르면 동일한 메세지 Toast가 계속 떠서 화면이 안 보이게 됩니다.
- Toast message로 중복 처리를 하여 같은 메세지 Toast는 한 번만 뜨게 합니다.

```ts
const showToast = (
  message: string,
  type: ToastType = "error",
  durationMs = 2500
) => {
  setToasts((prev) => {
    const alreadyExists = prev.some((t) => t.message === message); // 메세지로 중복 제거
    if (alreadyExists) return prev;

    return [...prev, { message, type, durationMs }];
  });
};
```

<br>
<br>

**<mark class="yellow">5\. 토스트 여러 개 띄우기</mark>**

- Network 에러와 mutation 동작에서 모두 Toast를 띄울 예정이기 때문에 Toast를 여러 개 띄워야 합니다.
- toasts 배열로 받고, 일정 시간이 지나면 closingToasts에 담아 toast를 제거합니다.

```tsx
const Toast = () => {
  const container = document.getElementById("toast");
  const toasts = useContext(ToastContext);
  const { closeToast } = useToast();

  const [closingToasts, setClosingToasts] = useState<string[]>([]);

  /**
   * toasts 중에서 durationMs가 지난 toast는 closingToasts에 담습니다.
   */
  useEffect(() => {
    const timers = toasts.map((toast) => {
      if (
        !closingToasts.includes(toast.message) &&
        Number.isFinite(toast.durationMs)
      ) {
        return setTimeout(() => {
          setClosingToasts((prev) => [...prev, toast.message]);
        }, toast.durationMs);
      }
    });

    return () => {
      timers.forEach(clearTimeout);
    };
  }, [toasts, closingToasts]);

  /**
   * closingToasts 중 완벽히 화면에서 사라진 toast는 제거합니다.
   */
  useEffect(() => {
    const timers = closingToasts.map((message) =>
      setTimeout(() => {
        closeToast(message);
        setClosingToasts((prev) => prev.filter((m) => m !== message));
      }, 400)
    );

    return () => {
      timers.forEach(clearTimeout);
    };
  }, [closingToasts, closeToast]);

  if (!container) return null;

  return createPortal(
    <S.ToastContainer>
      {toasts.map(({ message, type }) => (
        <S.Wrapper
          key={message}
          $type={type}
          $closeAnimation={closingToasts.includes(message)}
          role="alert"
          aria-live="assertive"
        >
          {message}
        </S.Wrapper>
      ))}
    </S.ToastContainer>,
    container
  );
};

export default Toast;
```

<br>
<br>

## <mark class="pink">🔥2. Network 에러</mark>

**<mark class="yellow">[레퍼런스]</mark>**

✅ 유튜브의 UX가 좋다고 판단하여 참고해서 개발하였습니다.

1\. 오프라인 상태로 클릭 event -> get 요청이면 로딩 중 표시 -> 온라인으로 바뀌면 자동 fetching  
2\. 오프라인 상태로 클릭 event -> 나머지 요청이면 에러 Toast 띄우기  
3\. 오프라인 상태로 라우팅 -> Fallback UI 보여주기

<br>
<br>

**<mark class="yellow">[문제점]</mark>**

🤔 navigator.onLine 문제

이전 버전에서는 navigator.onLine을 사용하여 네트워크 상태를 확인했습니다. 그러나 크롬 기반 브라우저에서는 잘 작동하지 않습니다. 거짓 네거티브와 관련된 많은 문제가 있으며, 이로 인해 쿼리가 오프라인으로 잘못 표시될 수 있었습니다.

탠스택 쿼리에서는 [networkMode](https://github.com/TanStack/query/blob/main/docs/framework/react/guides/network-mode.md) 속성이 있기 때문에 이를 위한 온라인/오프라인 판별 클래스가 있을 거라고 판단했습니다. 공식문서에서 [OnlineManager](https://github.com/TanStack/query/blob/main/docs/reference/onlineManager.md)를 사용한다는 것을 알았고 [onlineManager.ts 코드](https://github.com/TanStack/query/blob/main/packages/query-core/src/onlineManager.ts)를 참고하여 useNetwork 훅을 만들었습니다.

<br>

**useNetwork.ts**

window 객체의 `online`과 `offline` 이벤트를 사용하여 네트워크를 감지 가능

```ts
useEffect(() => {
  window.addEventListener("online", handleOnline);
  window.addEventListener("offline", handleOffline);

  return () => {
    window.removeEventListener("online", handleOnline);
    window.removeEventListener("offline", handleOffline);
  };
}, []);

return isOnline;
```

<br>
<br>

**<mark class="yellow">1. queries의 networkMode: "online"으로 변경</mark>**

> 💡 레퍼런스 1번 해결: get 요청이면 로딩 중 표시 -> 온라인으로 바뀌면 자동 fetching

networkMode "online": TanStack Query도 재시도 메커니즘을 일시 중지, 일시 중지된 쿼리는 네트워크 연결을 다시 얻으면 계속 실행  
-> networkMode를 "online"으로 변경하여 네트워크가 연결되었을 때 자동 fetching을 가능하게 했습니다.

<video controls>
  <source src="https://github.com/user-attachments/assets/83f1bc9f-b458-47c0-93b4-3030432e264a" type="video/mp4">
</video>

<br>
<br>

**<mark class="yellow">2. Network 오프라인이면 계속 Toast 띄우기</mark>**

> 💡 레퍼런스 2번 해결: 오프라인 상태로 클릭 event -> 나머지 요청이면 에러 Toast 띄우기

- 오프라인이면 Toast를 계속 띄우고, 온라인으로 바뀌면 오프라인 Toast를 닫고, 성공 Toast를 보여줍니다.
- post, put, delete 요청일 땐 에러 Toast를 띄웁니다.

**useNetwork.ts**

```ts
// 온라인이면 에러 토스트 닫고, 성공 토스트 띄우기
const handleOnline = () => {
  setIsOnline(true);
  closeToast(MESSAGES.ERROR.OFFLINE);
  showToast(MESSAGES.SUCCESS.ONLINE, "success");
};

// 오프라인이면 에러 토스트 무한으로 띄우기
const handleOffline = () => {
  setIsOnline(false);
  showToast(MESSAGES.ERROR.OFFLINE, "error", Infinity);
};
```

<video controls>
  <source src="https://github.com/user-attachments/assets/fdf65589-cb3b-4ddf-a47b-53b2ac546f5d" type="video/mp4">
</video>

<br>
<br>

**<mark class="yellow">3. 오프라인 상태로 페이지 이동하면 Fallback UI 보여주기</mark>**

> 💡 레퍼런스 3번 해결: 오프라인 상태로 라우팅 -> Fallback UI 보여주기

전역 ErrorBoundary에서 error를 잡아 `!isOnline`, `error.name === "ChunkLoadError"`, `NetworkError`일 때 새로고침 Fallback UI를 보여줍니다.

**ErrorBoundarySwitch.tsx**

```tsx
const ErrorBoundarySwitch = ({
  error,
  resetError,
}: ErrorBoundarySwitchProps) => {
  const isOnline = useNetwork();
  const queryClient = useQueryClient();

  const handleRetry = () => {
    resetError();
    queryClient.invalidateQueries({
      predicate: (query) => query.state.status === "error",
    });
  };

  switch (true) {
    case !isOnline || error.name === "ChunkLoadError":
    case error instanceof NetworkError:
      return <NetworkFallback onRetry={handleRetry} />; // 네트워크 에러
    case error instanceof ApiError:
      return <ApiFallback onRetry={handleRetry} errorMessage={error.message} />;
    default:
      return <DefaultFallback onRetry={handleRetry} />;
  }
};
```

![image](https://github.com/user-attachments/assets/1bee0eb4-dedb-4a2f-88e7-579c6b9525a0)
