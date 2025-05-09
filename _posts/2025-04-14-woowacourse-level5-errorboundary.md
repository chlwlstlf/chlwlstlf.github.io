---
layout: single
title: "[우테코] Level5 ErrorBoundary 전략 세우기"
categories: woowacourse
toc: true
toc_sticky: true
---

# 토큰 만료되면 재로그인 유도 모달 띄우기

<br>

관련 PR: [Errorboundary 전략 세우기, Network에 따른 UI 변경](https://github.com/woowacourse-teams/2024-corea/pull/814)

<br>

## <mark class="pink">🔥[문제점]</mark>

refresh 토큰이 만료되었을 때 alert 창이 뜨는 것이 UX에 안 좋다고 판단했습니다. 이를 Modal로 띄우기 위해 ErrorBoundary를 전략에 따라 다른 UI를 보여주기로 결정하였습니다.

<br>
<br>

## <mark class="pink">🔥[에러 종류]</mark>

**<mark class="yellow">1. AuthorizationError</mark>**

> 서비스 내에서 해결 가능 + 예상 가능

**apiClient.ts**

`/refresh` 요청의 모든 에러는 `AuthorizationError`로 간주합니다.

```ts
const refreshAccessToken = async (): Promise<string | undefined> => {
  const response = await fetch(`${serverUrl}${API_ENDPOINTS.REFRESH}`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    credentials: "include",
  });

  const data = await parseResponse(response);
  const newAccessToken = response.headers.get("Authorization");

  // refresh의 모든 에러는 재로그인 유도
  if (!response.ok) {
    isRefreshing = false;
    const error = new AuthorizationError(
      data?.message + MESSAGES.ERROR.POST_AUTH_AGAIN
    );
    processQueue(error, null);
    throw error;
  }

  if (newAccessToken) {
    localStorage.setItem("accessToken", newAccessToken);
    processQueue(null, newAccessToken);
    isRefreshing = false;

    return newAccessToken;
  }
};
```

<br>

그 외 api 요청: AccessToken 401 토큰 만료 외에 모든 401 에러는 AuthorizationError로 간주합니다.

```ts
if (!response.ok) {
  if (response.status === 401) {
    throw new AuthorizationError(
      data?.message + MESSAGES.ERROR.POST_AUTH_AGAIN
    );
  }

  throw new ApiError({
    message: data?.message || errorMessage,
    strategy,
    meta,
    status: response.status,
  });
}
```

<br>
<br>

**<mark class="yellow">2. NetworkError</mark>**

> 서비스 내에서 해결 불가능 + 예상 가능

사용자가 직접 네트워크 연결을 다시 해야 하므로 오프라인을 알려주는 Toast를 계속 띄웁니다.  
온라인으로 연결 되면 오프라인 Toast를 닫고 온라인 Toast를 보여줍니다.

<br>
<br>

**<mark class="yellow">3. ApiError(403, 404)</mark>**

> 해결 불가능 + 예상 가능

전역 ErrorBoundary에서 Sentry에 로깅합니다.

<br>
<br>

**<mark class="yellow">4. ApiError(500, 503) 및 예측 불가능한 일반 Error</mark>**

> 해결 불가능 + 예상 불가능

전역 ErrorBoundary에서 Sentry에 로깅합니다.

<br>
<br>

## <mark class="pink">🔥[흐름 설명]</mark>

**<mark class="yellow">1. GET 요청</mark>**

- `useSuspenseQuery`를 사용하고 있기 때문에 무조건 `ErrorBoundary`로 넘어갑니다. 데이터 fetching을 하지 못 했을 때 그 부분을 대신하는 Fallback UI가 뜨는 것이 당연하다고 생각하여 다른 전략으로 변경하지 않았습니다.
- 이때 오류를 더 강조하는 의도로 `Toast`나 `Modal`을 추가적으로 띄울 수 있습니다.

![image](https://github.com/user-attachments/assets/088af0bb-74ca-4f10-a86b-642895ad047e)

<br>
<br>

**<mark class="yellow">2. POST, PUT, DELETE 요청</mark>**

- `mutation` 에러는 `mutationCache`의 onError로 넘어갑니다.
- 여기서 error를 추가하면, `ErrorManager`의 useEffect에서 error 변화를 감지하고 해당 전략에 맞게 UI를 띄웁니다.

![image](https://github.com/user-attachments/assets/c9e7c18c-1b7a-46c7-9c35-4a892f3dae29)

<br>
<br>

## <mark class="pink">🔥1. 에러 수집하기</mark>

**ErrorProvider.tsx**

context api로 전역에서 발생하는 error 수집합니다.

```tsx
import type { PropsWithChildren } from "react";
import { createContext, useState } from "react";

export const ErrorContext = createContext<Error | null>(null);
export const ErrorDispatchContext = createContext<
  (error: Error | null) => void
>(() => {});

export const ErrorProvider = ({ children }: PropsWithChildren) => {
  const [error, setError] = useState<Error | null>(null);

  return (
    <ErrorContext.Provider value={error}>
      <ErrorDispatchContext.Provider value={setError}>
        {children}
      </ErrorDispatchContext.Provider>
    </ErrorContext.Provider>
  );
};
```

<br>

**useErrorStore.ts**

```ts
import { useContext } from "react";
import { ErrorContext, ErrorDispatchContext } from "@/providers/ErrorProvider";

const useErrorStore = () => {
  const error = useContext(ErrorContext);
  const setError = useContext(ErrorDispatchContext);

  if (error === undefined || setError === undefined) {
    throw new Error("ErrorProvider 내부에서만 해당 훅을 사용할 수 있어요");
  }

  return { error, setError };
};

export default useErrorStore;
```

<br>

**ErrorModalProvider.tsx**

Toast와 비슷한 방식으로 전역에서 `openErrorModal`로 모달을 띄우고 싶어서 `ErrorModalProvider`를 만들었습니다.

```tsx
export type ErrorModalState = {
  isOpen: boolean;
  message: string;
  onConfirm?: () => void;
  onCancel?: () => void;
  confirmButtonText?: string;
  cancelButtonText?: string;
};

export const ModalContext = createContext<ErrorModalState | null>(null);
export const ModalDispatchContext = createContext<
  Dispatch<SetStateAction<ErrorModalState>>
>(() => {});

export const ErrorModalProvider = ({ children }: { children: ReactNode }) => {
  const [modalInfo, setModalInfo] = useState<ErrorModalState>({
    isOpen: false,
    message: "",
  });

  return (
    <ModalContext.Provider value={modalInfo}>
      <ModalDispatchContext.Provider value={setModalInfo}>
        {children}
        <ConfirmModal
          isOpen={modalInfo.isOpen}
          onClose={() => {
            setModalInfo((prev) => ({ ...prev, isOpen: false }));
            modalInfo.onCancel?.();
          }}
          onConfirm={() => {
            setModalInfo((prev) => ({ ...prev, isOpen: false }));
            modalInfo.onConfirm?.();
          }}
          onCancel={() => {
            setModalInfo((prev) => ({ ...prev, isOpen: false }));
            modalInfo.onCancel?.();
          }}
          confirmButtonText={modalInfo.confirmButtonText}
          cancelButtonText={modalInfo.cancelButtonText}
        >
          {modalInfo.message}
        </ConfirmModal>
      </ModalDispatchContext.Provider>
    </ModalContext.Provider>
  );
};
```

<br>

**index.tsx**

```tsx
ReactDOM.createRoot(document.getElementById("root") as HTMLElement).render(
  <React.StrictMode>
    <ThemeProvider theme={theme}>
      // ThemeProvider 안에 ErrorProvider 추가
      <ErrorProvider>
        <QueryProvider>
          <ToastProvider>
            // Error 전용 모달 띄우는 Provider 추가
            <ErrorModalProvider>
              <ErrorManager>
                <GlobalStyles />
                <ReactQueryDevtools initialIsOpen={false} />
                <Toast />
                <RouterProvider router={router} />
              </ErrorManager>
            </ErrorModalProvider>
          </ToastProvider>
        </QueryProvider>
      </ErrorProvider>
    </ThemeProvider>
  </React.StrictMode>
);
```

<br>
<br>

## <mark class="pink">🔥2. 에러 감지하기</mark>

**ErrorManager.tsx**

1\. Query (Suspense 기반)

- useSuspenseQuery 사용 시, 에러는 자동으로 ErrorBoundary로 전달됩니다.
- fallback UI는 ErrorBoundary가 처리하지만, 전략에 따라 사용자에게 추가 알림(Toast, Modal, Redirect)이 가능합니다.

2\. Mutation

- 기본 에러 전략은 TOAST입니다.
- 전략이 ERROR_BOUNDARY 또는 IGNORE인 경우, 화면이나 토스트에 아무 처리도 일어나지 않습니다.
- ※ 모든 CustomError는 ErrorManager를 통해 처리되며, strategy 기반으로 UI 반영됩니다.

<br>

**ErrorManager.tsx**

```tsx
const ErrorManager = ({ children }: { children: React.ReactNode }) => {
  const { error, setError } = useErrorStore();
  const { showToast } = useToast();
  const { openErrorModal } = useErrorModal();

  useEffect(() => {
    if (!error) return;

    if (!(error instanceof CustomError)) {
      throw error;
    }

    switch (error.strategy) {
      case ERROR_STRATEGY.TOAST:
        showToast(error.message);
        break;

      case ERROR_STRATEGY.MODAL: {
        const { onConfirm, onCancel, confirmButtonText, cancelButtonText } =
          error.meta ?? {};

        openErrorModal({
          message: error.message,
          onConfirm,
          onCancel,
          confirmButtonText,
          cancelButtonText,
        });
        break;
      }

      case ERROR_STRATEGY.REDIRECT: {
        const redirectTo = error.meta?.redirectTo ?? "/";
        window.location.href = redirectTo;
        break;
      }

      default:
        throw error;
    }

    setError(null);
  }, [error]);

  return children;
};

export default ErrorManager;
```

<br>
<br>

## <mark class="pink">🔥3. 에러 전략 세우기</mark>

**<mark class="yellow">1. default 전략 설명</mark>**

apiClient의 request 함수에서 get 요청은 Errorboundary, 나머지 요청은 Toast로 설정합니다.

**apiClient.ts**

```ts
request: async ({
  method,
  endpoint,
  headers = {},
  body = null,
  errorMessage = "",
  strategy = method === "GET" ? ERROR_STRATEGY.ERROR_BOUNDARY : ERROR_STRATEGY.TOAST, // GET은 ERROR_BOUNDARY, 나머지는 TOAST
  meta,
}: RequestProps) => {
  const requestInit = createRequestInit(method, headers, body);
  return await fetchWithToken(endpoint, requestInit, errorMessage, strategy, meta);
},
```

<br>

**<mark class="yellow">2. Toast 전략</mark>**

error가 가지고 있는 message를 띄웁니다.

```ts
await apiClient.post({
  endpoint: "엔드포인트",
  errorMessage: "메세지",
  strategy: ERROR_STRATEGY.TOAST,
});
```

<br>

**<mark class="yellow">3. Modal 전략</mark>**

확인 버튼, 취소 버튼, 확인 동작, 취소 동작을 커스텀할 수 있습니다.

```ts
await apiClient.post({
  endpoint: "엔드포인트",
  errorMessage: "메세지",
  strategy: ERROR_STRATEGY.MODAL,
  meta: {
    confirmButtonText: "확인",
    cancelButtonText: "취소",
    onConfirm: () => {},
    onCancel: () => {},
  },
});
```

<br>

**<mark class="yellow">4. Redirect 전략</mark>**

redirectTo: redirect 시킬 url

```ts
await apiClient.post({
  endpoint: "엔드포인트",
  errorMessage: "메세지",
  strategy: ERROR_STRATEGY.MODAL,
  meta: {
    redirectTo: "/",
  },
});
```

<br>
<br>

## <mark class="pink">🔥4. CustomError 만들기</mark>

기존 Error는 `message`만 인자로 갖고 있기 때문에 이를 확장하여 `strategy`와 `status` 그리고 다양한 옵션을 줄 수 있는 `meta` 인자를 추가로 받는 `CustomError`를 만들었습니다.

**CustomError.ts**

```ts
export type ErrorHandlingStrategy =
  (typeof ERROR_STRATEGY)[keyof typeof ERROR_STRATEGY];

export type CustomErrorMeta = {
  confirmButtonText?: string;
  cancelButtonText?: string;
  onConfirm?: () => void;
  onCancel?: () => void;
  redirectTo?: string;
};

export interface CustomErrorOptions {
  message: string;
  strategy?: ErrorHandlingStrategy;
  meta?: CustomErrorMeta;
  status?: number;
}

export class CustomError extends Error {
  strategy: ErrorHandlingStrategy;
  meta?: CustomErrorMeta;
  status?: number;

  constructor({
    message,
    strategy = ERROR_STRATEGY.ERROR_BOUNDARY,
    meta,
    status,
  }: CustomErrorOptions) {
    super(message);
    this.name = "CustomError";
    this.strategy = strategy;
    this.meta = meta;
    this.status = status;
  }
}
```

<br>

**<mark class="yellow">1. ApiError</mark>**

ApiError는 정해진 에러 전략이 없습니다. 사용자가 커스텀할 수 있습니다.

**CustomError.ts**

```ts
export class ApiError extends CustomError {
  constructor(options: CustomErrorOptions) {
    super(options);
    this.name = "ApiError";
  }
}
```

<br>

**<mark class="yellow">2. AuthorizationError</mark>**

가장 중요한 `AuthorizationError` 에러입니다. 사실 이것을 위해 여기까지 달려온 것인데요,,🤔

이전 flow는 토큰이 만료되면 1) alert 창이 뜨고, 2) 사용자가 로그인 버튼을 눌러야 했습니다. 하지만 이렇게 되면 사용자는 직접 로그인 버튼을 찾아서 눌러야 하고 서비스 이용 흐름이 끊기게 됩니다.

이를 개선하기 위해 토큰이 만료되면 바로 1) 재로그인 유도 모달이 뜨게 했습니다.

<br>

위에서 이런 코드를 보았을 것입니다.

**apiClient.ts**

```ts
// refresh의 모든 에러는 재로그인 유도
if (!response.ok) {
  isRefreshing = false;
  const error = new AuthorizationError(
    data?.message + MESSAGES.ERROR.POST_AUTH_AGAIN
  );
  processQueue(error, null);
  throw error;
}
```

<br>

`AuthorizationError`를 던지면 어떤 일이 일어날까요?

<br>

**CustomError.ts**

확인 버튼의 문구는 "로그인하기"로 동작은 "로컬스토리지 비우기", "github 로그인 페이지로 이동"으로 커스텀하였습니다.  
취소 버튼의 문구는 "나중에 하기"로 동작은 "로컬스토리지 비우기", "홈으로 이동"으로 커스텀하였습니다.

```ts
export class AuthorizationError extends CustomError {
  constructor(message: string) {
    super({
      message,
      strategy: ERROR_STRATEGY.MODAL,
      meta: {
        confirmButtonText: "로그인하기",
        cancelButtonText: "나중에 하기",
        onConfirm: () => {
          localStorage.clear();
          window.location.href = githubAuthUrl;
        },
        onCancel: () => {
          localStorage.clear();
          window.location.href = "/";
        },
      },
    });
    this.name = "AuthorizationError";
  }
}
```

<br>

**결과 화면**

이제 서비스 내에서 해결 가능하고 예상 가능한 `AuthorizationError`는 재로그인 유도 모달로 사용자의 흐름을 50% 개선하였습니다.

![image](https://github.com/user-attachments/assets/6fca7c01-3719-4067-a287-526f77d40bfe)

<br>

**<mark class="yellow">3. NetworkError</mark>**

NetworkError는 toast를 보여주어 사용자가 빠르게 네트워크 연결을 할 수 있게 유도합니다.

```ts
export class NetworkError extends CustomError {
  constructor(message: string) {
    super({ message, strategy: ERROR_STRATEGY.TOAST });
    this.name = "NetworkError";
  }
}
```
