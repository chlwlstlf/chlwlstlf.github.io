---
layout: single
title: "[우테코] Level3 Refresh Token"
categories: woowacourse
toc: true
toc_sticky: true
---

# Refresh Token으로 Access Token 갱신하기

## <mark class="pink">📌JWT란?</mark>

JWT 토큰은 클라이언트와 서버 간의 인증과 정보 교환을 위한 데이터 형식입니다. Header, Payload, Signature로 구성됩니다.

Header: 토큰의 타입(JWT)과 암호화 알고리즘  
Payload: 유저의 신원 정보나 권한 정보와 같은 데이터를 포함  
Signature: Header와 Payload를 서버의 비밀키(대칭키) 또는 개인키(비대칭키)로 서명한 값이 담겨있음

<br>
<br>

## <mark class="pink">📌JWT의 문제</mark>

**<mark class="yellow">1. 탈취(Man-in-the-Middle)</mark>**

JWT 토큰이 탈취되면, 공격자는 그 토큰을 사용해 인증된 사용자처럼 서버에 접근할 수 있습니다.  
서버는 JWT의 서명을 검증할 수 있지만, 누가 해당 토큰을 사용 중인지는 구분할 수 없습니다. 따라서 탈취된 토큰을 막을 수 없습니다.

<br>

**<mark class="yellow">2. 유효기간 관리의 어려움</mark>**

JWT는 기본적으로 "무상태(Stateless)"이므로, 서버는 토큰을 생성한 이후 만료 전까지 토큰 자체를 철회하거나 무효화할 수 없습니다. (단, 예외적으로 Redis와 같은 저장소를 이용해 블랙리스트를 구현할 수 있습니다).

<br>

**<mark class="yellow">결론</mark>**

유효기간이 **짧으면 사용자 경험(UX)**이 저하됩니다. (사용자가 로그인을 자주 요구받음)  
유효기간이 **길면 보안 위험**이 커집니다. (탈취 시 오랜 시간 동안 유효)

<br>
<br>

## <mark class="pink">📌해결법</mark>

**<mark class="yellow">Access Token과 Refresh Token의 조합</mark>**

JWT 기반 인증 시스템에서 보안과 사용자 경험의 균형을 맞추기 위해 다음 방식을 사용합니다.

<br>

**<mark class="yellow">Access Token</mark>**

유효기간이 짧은 토큰입니다. (몇 분~몇 시간)  
클라이언트가 API 요청을 보낼 때마다 이 토큰을 사용하여 인증합니다.

👍 장점: 탈취되더라도 짧은 시간 내에 만료되어 피해를 줄일 수 있습니다.  
👎 단점: 유효기간이 짧기 때문에 갱신을 자주 해야 합니다.

<br>

**<mark class="yellow">Refresh Token</mark>**

유효기간이 긴 토큰입니다. (일주일~일년)  
Access Token이 만료되었을 때, 클라이언트는 이 토큰을 서버로 보내 새로운 Access Token을 발급받습니다.  
Refresh Token은 보통 서버의 데이터베이스에 저장하거나 관리하여 탈취를 방지합니다.
Refresh Token 만료 시엔 재로그인을 하여 새로운 Refresh Token을 받아야 합니다.

👍 장점: Access Token의 만료를 관리하면서도 사용자가 다시 로그인하지 않아도 됩니다.  
👎 단점: Refresh Token이 탈취되면 장기적으로 악용될 가능성이 있습니다.

<br>
<br>

## <mark class="pink">📌JWT 인증 방식 흐름</mark>

사용자가 로그인하면, 서버는 Access Token과 Refresh Token을 발급합니다.

<br>

**<mark class="yellow">클라이언트</mark>**

API 요청 시 Access Token을 헤더에 포함하여 보냅니다.  
Access Token이 만료되면, Refresh Token을 사용해 새로운 Access Token을 발급받습니다.

<br>

**<mark class="yellow">서버</mark>**

Refresh Token 요청 시 Refresh Token의 유효성을 검증합니다.  
⭕ 유효하다면, 새로운 Access Token을 생성합니다.  
❌ 유효하지 않다면, 로그아웃을 강제하거나 재로그인을 요구합니다.

<br>

![1](https://github.com/user-attachments/assets/199ae9f6-f63c-4abe-984b-a1f72293f49b)

<br>
<br>

## <mark class="pink">🔥Refresh Token을 도입한 이유</mark>

프로젝트에 도입한 이유는 사실 다양한 경험을 해보고 싶었기 때문입니다. FE에서 Refresh Token을 구현하는 것이 조금 어렵다는 글을 보았고, 프로젝트에서 그 경험을 하고 싶었습니다.

그 후 많은 서비스에서 Refresh Token을 도입한 이유에 대해 찾아보았을 땐 **보안과 사용자 경험(UX)**의 균형을 맞추기 위함이라고 하였습니다.

<br>

**보안**

- Access Toke은 탈취 위험이 있으므로 유효기간을 짧게 설정하는 것이 일반적입니다.
- 하지만 이 경우, 사용자는 자주 로그인이 풀리는 불편을 겪게 됩니다.
- 이 문제를 해결하기 위해, 만료된 Access Token을 안전하게 갱신할 수 있는 수단으로 Refresh Token이 사용됩니다.

**사용자 경험**

- 사용자는 로그인 후 오랜 시간 동안 재로그인 없이 서비스를 이용하길 원합니다.
- Refresh Token을 사용하면 로그인을 다시 하지 않고도 Access Token을 자동으로 재발급할 수 있어, UX가 훨씬 매끄러워집니다.

<br>
<br>

## <mark class="pink">🔥토큰 만료 시간</mark>

Access Token은 **30분**으로 짧게 설정했습니다. 탈취 시 공격자가 사용할 수 있는 기간을 제한하기 위함이었습니다.

Refresh Token은 **일주일**로 설정했습니다. 도메인이 코드 리뷰인데 이 활동은 일주일을 넘기지 않을 것으로 판단하고 활동을 마친 사이클일 때마다 로그인을 하는 것이 좋을 것 이라고 판단하였습니다.

<div class="blue-box">
  <b>참고</b>
  <div>Google OAuth: Access Token은 1시간, Refresh Token은 최대 6개월.</div>
  <div>GitHub: Access Token은 1시간, Refresh Token은 사용자가 설정한 기간.</div>
</div>

<br>
<br>

## <mark class="pink">📌apiClient.ts 코드 설명</mark>

Refresh Token 도입 전 Access Token을 localStorage로 가지고 있었을 때의 코드입니다.

`createRequestInit` 함수는 HTTP 요청을 보낼 때 필요한 RequestInit 객체를 생성하는 역할을 합니다. 이 객체는 fetch API에 요청을 보낼 때 사용됩니다.

`fetchWithErrorHandling` 함수는 실제 HTTP 요청을 보내고 응답을 처리하는 역할을 합니다. 이 함수는 fetch API를 사용하여 네트워크 요청을 보내고, 응답을 확인한 후, 오류가 있으면 적절한 오류를 던집니다. 또한, 오프라인 상태에서 요청을 보내지 않도록 체크합니다.

여기서 Refresh token과 Access Token 갱신 로직을 추가해보겠습니다.

```ts
import { serverUrl } from "@/config/serverUrl";
import MESSAGES from "@/constants/message";

type Method = "GET" | "POST" | "PATCH" | "DELETE";

interface ApiProps {
  endpoint: string;
  headers?: Record<string, string>;
  body?: object | null;
  errorMessage?: string;
}

interface RequestProps extends ApiProps {
  method: Method;
}

const createRequestInit = (
  method: Method,
  headers: Record<string, string>,
  body: object | null
): RequestInit => {
  const token = localStorage.getItem("accessToken");

  return {
    method,
    headers: {
      ...headers,
      Authorization: token ? `Bearer ${token}` : "",
      "Content-Type": "application/json",
    },
    body: body ? JSON.stringify(body) : null,
  };
};

const fetchWithErrorHandling = async (
  endpoint: string,
  requestInit: RequestInit,
  errorMessage: string = ""
) => {
  // 오프라인 확인
  if (!navigator.onLine) {
    throw new Error(MESSAGES.ERROR.OFFLINE);
  }

  const response = await fetch(`${serverUrl}${endpoint}`, requestInit);

  if (!response.ok) {
    throw new Error(
      errorMessage || `Error: ${response.status} ${response.statusText}`
    );
  }

  const text = await response.text();

  return text ? JSON.parse(text) : response;
};

const apiClient = {
  get: ({ endpoint, headers = {}, errorMessage = "" }: ApiProps) => {
    return apiClient.request({
      method: "GET",
      endpoint,
      headers,
      errorMessage,
    });
  },
  post: ({
    endpoint,
    headers = {},
    body = {},
    errorMessage = "",
  }: ApiProps) => {
    return apiClient.request({
      method: "POST",
      endpoint,
      headers,
      body,
      errorMessage,
    });
  },
  patch: ({
    endpoint,
    headers = {},
    body = {},
    errorMessage = "",
  }: ApiProps) => {
    return apiClient.request({
      method: "PATCH",
      endpoint,
      headers,
      body,
      errorMessage,
    });
  },
  delete: ({ endpoint, headers = {}, errorMessage = "" }: ApiProps) => {
    return apiClient.request({
      method: "DELETE",
      endpoint,
      headers,
      errorMessage,
    });
  },
  request: async ({
    method,
    endpoint,
    headers = {},
    body = null,
    errorMessage = "",
  }: RequestProps) => {
    const requestInit = createRequestInit(method, headers, body);
    return await fetchWithErrorHandling(endpoint, requestInit, errorMessage);
  },
};

export default apiClient;
```

<br>
<br>

## <mark class="pink">🔥처음 작성했던 잘못된 코드</mark>

401 상태가 발생하면 `refreshAccessToken` 함수에서 새로운 accessToken을 갱신합니다.  
이후 모든 요청은 localStorage에서 갱신된 accessToken을 읽어 사용하기 때문에, 일반적으로는 정상적으로 동작합니다.

그러나 여러 요청이 연이어 발생하는 상황에서, **accessToken이 아직 갱신되기 전에 다른 요청이 기존 토큰으로 서버에 도달하면** 해당 요청은 401 에러를 다시 발생시킬 수 있습니다.  
즉, 단순히 요청이 동시에 발생했기 때문이 아니라, **토큰 갱신과 요청 처리 사이의 비동기 타이밍 문제**로 인해 예기치 않은 에러가 발생할 수 있습니다.

<br>

**apiClient.ts**

```ts
const refreshAccessToken = async (): Promise<string | undefined> => {
  const refreshToken = localStorage.getItem("refreshToken");

  const response = await fetch(`${serverUrl}${API_ENDPOINTS.REFRESH}`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({ refreshToken }),
  });

  const text = await response.text();
  const data = text ? JSON.parse(text) : null;

  const newAccessToken = response.headers.get("Authorization");

  if (!response.ok) {
    if (response.status === 401) {
      alert("토큰이 만료되었습니다. 다시 로그인 해주세요!");
      localStorage.clear();
      window.location.href = "/";
    } else {
      throw new HTTPError(data.message || MESSAGES.ERROR.POST_REFRESH);
    }
  } else if (newAccessToken) {
    // localStorage에 새로운 토큰을 저장하기 전에 api를 호출하면 401이 뜸 - Race Condition
    localStorage.setItem("accessToken", newAccessToken);
    return newAccessToken;
  }
};

const fetchWithToken = async (
  endpoint: string,
  requestInit: RequestInit,
  errorMessage: string = ""
) => {
  if (!navigator.onLine) {
    throw new HTTPError(MESSAGES.ERROR.OFFLINE);
  }

  let response = await fetch(`${serverUrl}${endpoint}`, requestInit);
  let text = await response.text();
  let data = text ? JSON.parse(text) : null;

  if (response.status === 401 && data.message === "토큰이 만료되었습니다.") {
    const newAccessToken = await refreshAccessToken(); // 401이면 Access Token 갱신
    requestInit.headers = {
      ...requestInit.headers,
      Authorization: `Bearer ${newAccessToken}`,
    };

    response = await fetch(`${serverUrl}${endpoint}`, requestInit);
    text = await response.text();
    data = text ? JSON.parse(text) : null;

    if (!response.ok && response.status !== 401) {
      throw new HTTPError(data.message || MESSAGES.ERROR.POST_REFRESH);
    }
  }

  if (!response.ok && response.status !== 401) {
    throw new HTTPError(data.message || errorMessage);
  }

  return text ? data : response;
};
```

<br>

**<mark class="yellow">결과 화면</mark>**

![Image](https://github.com/user-attachments/assets/9b81a93c-6ac8-4c59-8687-3dcd38a61e7a)

1\. `refreshAccessToken`에 임의로 `await new Promise((resolve) => setTimeout(resolve, 3000));` 코드를 추가해 3초간 지연시켰습니다. 토큰 갱신이 너무 빨라 Race Condition 상황을 관찰하기 어려워, 의도적으로 딜레이를 주었습니다.

2\. Access Token이 만료된 상태에서 여러 요청이 거의 동시에 발생하면, 각 요청이 개별적으로 `refreshAccessToken()`을 호출하게 됩니다.

3\. 이때 하나의 요청이 새로운 토큰을 저장하기 전에 다른 요청이 여전히 만료된 기존 토큰으로 재요청하면, 이들 요청도 401을 발생시키고 다시 refresh를 시도합니다.  
이로 인해 **요청 수만큼 중복 갱신이 발생하고, 모든 요청이 401 → refresh를 반복하는 비효율적인 상황**이 발생합니다.

<br>
<br>

## <mark class="pink">🔥개선된 코드</mark>

그럼 어떻게 이 문제를 해결할 수 있을까요?

토큰 갱신 작업 중이면 다른 api 요청을 잠시 보류해둘 수 있습니다.

<br>

**<mark class="yellow">개선된 설계의 핵심</mark>**

`Race Condition`을 방지하기 위해서는 `전역 Promise 관리`를 사용하여 refreshAccessToken이 한 번만 호출되도록 해야 합니다.

이미 갱신 중이라면 추가로 갱신 요청을 보내지 않도록 해야 하고,  
갱신 중인 작업이 완료될 때까지 다른 api 요청은 대기하도록 코드를 수정해야 합니다.

이는 **isRefreshing** 플래그와 **failedQueue**를 사용해서 해결할 수 있습니다.

<br>

**<mark class="yellow">요청 흐름</mark>**

**전역 변수**  
1\. 초기 isRefreshing는 false, failedQueue는 빈 배열

**fetchWithToken 함수**  
2\. 첫 번째 요청이 401 상태를 받으면  
3\. isRefreshing를 true로 변경하고  
4\. refreshAccessToken 실행

**refreshAccessToken 함수**  
_[토큰 갱신 실패했을 때]_  
5\. refresh Token으로 새로운 Access Token 받기  
6\. refresh token도 만료 됐다면 재로그인 유도, localStorage 초기화, 새로고침  
7\. processQueue 함수를 실행하여 대기 중이던 모든 요청에 error를 전달, isRefreshing 초기화

_[토큰 갱신 성공했을 때]_  
8\. 정상적으로 새로운 Access Token을 받았으면 대기 중이던 모든 요청에 새로운 토큰 전달, isRefreshing 초기화

**다시 fetchWithToken 함수**  
_[첫 번째 api 요청]_  
9\. 새로운 Access Token으로 첫 번째 api 재요청

_[두 번째 api 요청부터]_  
10\. 토큰 갱신 중인 상태면 요청의 resolve와 reject를 failedQueue에 추가, 두 번째 요청부터 failedQueue에 추가됨, 이 Promise는 processQueue에서 resolve/reject될 때까지 대기  
11\. processQueue가 호출되어 Promise가 resolve되면 then 블록 실행, token은 새 Access Token이며, 이를 사용해 요청을 재실행

<br>

**<mark class="yellow">apiClient.ts</mark>**

```ts
interface QueueItem {
  resolve: (value: string | PromiseLike<string>) => void;
  reject: (reason?: Error) => void;
}

// 1. 초기 isRefreshing는 false, failedQueue는 빈 배열
let isRefreshing = false;
let failedQueue: QueueItem[] = [];

const processQueue = (
  error: Error | null = null,
  token: string | null = null
) => {
  failedQueue.forEach((prom) => {
    if (error) {
      prom.reject(error); // 대기 중 요청들에 에러 전달
    } else {
      prom.resolve(token as string); // 대기 중 요청들에 새 토큰 전달
    }
  });

  failedQueue = [];
};

const refreshAccessToken = async (): Promise<string | undefined> => {
  const refreshToken = localStorage.getItem("refreshToken");

  // 5. refresh Token으로 새로운 Access Token 받기
  const response = await fetch(`${serverUrl}${API_ENDPOINTS.REFRESH}`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({ refreshToken }),
  });

  const text = await response.text();
  const data = text ? JSON.parse(text) : null;

  const newAccessToken = response.headers.get("Authorization");

  if (!response.ok) {
    const errorMessage = data.message || MESSAGES.ERROR.POST_REFRESH;
    let error;

    if (response.status === 401) {
      // 6. refresh token도 만료 됐다면 재로그인 유도, localStorage 초기화, 새로고침
      error = new AuthorizationError(errorMessage);
      alert("토큰이 만료되었습니다. 다시 로그인 해주세요!");
      localStorage.clear();
      window.location.href = "/";
    } else {
      error = new HTTPError(errorMessage);
    }

    // 7. processQueue 함수를 실행하여 대기 중이던 모든 요청에 error를 전달, isRefreshing 초기화
    processQueue(error, null);
    isRefreshing = false;
    throw error;
  }

  // 8. 정상적으로 새로운 Access Token을 받았으면 대기 중이던 모든 요청에 새로운 토큰 전달, isRefreshing 초기화
  if (newAccessToken) {
    localStorage.setItem("accessToken", newAccessToken);
    processQueue(null, newAccessToken);
    isRefreshing = false;
    return newAccessToken;
  }
};

const fetchWithToken = async (
  endpoint: string,
  requestInit: RequestInit,
  errorMessage: string = ""
) => {
  if (!navigator.onLine) {
    throw new HTTPError(MESSAGES.ERROR.OFFLINE);
  }

  let response = await fetch(`${serverUrl}${endpoint}`, requestInit);
  let text = await response.text();
  let data = text ? JSON.parse(text) : null;

  // 2. 첫 번째 요청이 401 상태를 받으면
  if (response.status === 401 && data.message === "토큰이 만료되었습니다.") {
    // 10. 토큰 갱신 중인 상태면 요청의 resolve와 reject를 failedQueue에 추가(두 번째 요청부터 failedQueue에 추가됨)
    // 이 Promise는 processQueue에서 resolve/reject될 때까지 대기
    if (isRefreshing) {
      return new Promise<string>((resolve, reject) => {
        failedQueue.push({ resolve, reject });
      }).then(async (token) => {
        // 11. processQueue가 호출되어 Promise가 resolve되면 then 블록 실행
        // token은 새 Access Token이며, 이를 사용해 요청을 재실행
        requestInit.headers = {
          ...requestInit.headers,
          Authorization: `Bearer ${token}`,
        };

        response = await fetch(`${serverUrl}${endpoint}`, requestInit);
        text = await response.text();
        data = text ? JSON.parse(text) : null;

        if (!response.ok && response.status !== 401) {
          throw new HTTPError(data.message || MESSAGES.ERROR.POST_REFRESH);
        }

        return text ? data : response;
      });
    }

    // 3. isRefreshing를 true로 변경
    isRefreshing = true;

    // 4. refreshAccessToken 실행
    const newAccessToken = await refreshAccessToken();
    requestInit.headers = {
      ...requestInit.headers,
      Authorization: `Bearer ${newAccessToken}`,
    };

    // 9. 새로운 Access Token으로 첫 번째 api 재요청
    response = await fetch(`${serverUrl}${endpoint}`, requestInit);
    text = await response.text();
    data = text ? JSON.parse(text) : null;

    if (!response.ok && response.status !== 401) {
      throw new HTTPError(data.message || MESSAGES.ERROR.POST_REFRESH);
    }
  }

  if (!response.ok && response.status !== 401) {
    throw new HTTPError(data.message || errorMessage);
  }

  return text ? data : response;
};
```

<br>

**<mark class="yellow">결과 화면</mark>**

![Image](https://github.com/user-attachments/assets/de6735cd-febe-4220-a602-77c3eae006a2)

1\. 이번에도 refreshAccessToken을 3초 delay를 시켜 실험해보았습니다.

2\. Access Token이 만료된 상태에서 여러 API 요청이 동시에 발생하면, 각 요청은 401 에러를 받고 refreshAccessToken을 호출하려 합니다.

3\. 이때 isRefreshing이 true인 경우에는 새 토큰을 기다리는 중이라는 뜻이므로, 해당 요청은 failedQueue에 저장되어 재시도 타이밍을 지연시킵니다.

4\. refreshAccessToken이 완료되면 `processQueue(null, newAccessToken)`을 실행하여,
failedQueue에 쌓여있던 요청들의 resolve 콜백을 호출하고, 각 요청은 갱신된 Access Token으로 재시도를 진행합니다.

![Image](https://github.com/user-attachments/assets/eee4470d-3f4d-4163-ae13-02142b5b8f0a)

<br>

**<mark class="yellow">이 코드의 장점</mark>**

이렇게 구현하면 하나의 토큰 재발급 요청만 보내도록 제어할 수 있으며, 동시에 발생한 요청들이 중복으로 refresh 요청을 보내는 `Race Condition` 문제를 방지할 수 있습니다.

특히 failedQueue는 일종의 요청 재시도 대기열 역할을 하며, refresh가 끝난 뒤 한꺼번에 처리되기 때문에 안전하고 효율적인 흐름을 만들 수 있습니다.

<br>
<br>

## <mark class="pink">🔥코드 추가 설명</mark>

**<mark class="yellow">Q1. failedQueue는 Promise 형태인가?</mark>**

아닙니다. failedQueue는 Promise 객체 자체를 저장하지 않고, resolve와 reject 함수만 저장합니다.  
Promise 객체는 해당 함수들을 호출하여 원하는 시점에 완료되거나 에러로 처리되도록 제어할 수 있습니다.

<br>

<mark class="yellow">Q2. Promise의 pending 상태는 고려할 필요가 없는가?</mark>

`failedQueue`에는 Promise 객체가 직접 들어가는 것이 아니라, 각각의 요청을 처리할 resolve와 reject 함수가 들어갑니다.  
이 함수들은 refreshAccessToken이 완료되었을 때 processQueue를 통해 실행되며, 그 순간 각 요청의 상태가 확정됩니다.

따라서 여기서 고려해야 할 것은 각 요청의 Promise 상태가 아니라, 공통으로 사용되는 **refreshAccessToken의 pending 상태가 길어질 경우입니다.**  
예기치 않게 refreshAccessToken이 resolve나 reject되지 않고 계속 pending 상태에 머물면, failedQueue에 쌓인 요청들도 처리되지 않은 채 멈춰 있게 되므로, 이를 방어하기 위한 타임아웃 처리나 fallback 전략이 필요합니다.

<br>
<br>

## <mark class="pink">🔥Refresh Token 쿠키에 저장 (25.02.19)</mark>

**<mark class="yellow">1. 저장 위치 변경한 이유</mark>**

Refresh Token을 쿠키에 저장하는 것으로 변경했습니다. 그 이유는 아래와 같습니다.

Access Token보다 Refresh Token이 만료 기간이 깁니다. 따라서 Refresh Token이 탈취되었을 때 보안적 이슈가 더 커지고 Refresh Token과 Access Token의 저장 위치를 다르게 하여 보안을 강하게 하려고 했습니다.

<br>

**<mark class="yellow">2. 왜 쿠키인가?</mark>**

**1\. LocalStorage의 취약점**

- JavaScript로 완전히 접근 가능 → XSS 공격 시 토큰 탈취 위험

**2\. HttpOnly 쿠키의 장점**

- 클라이언트 스크립트에서 읽을 수 없도록 차단 → XSS 방어

**3\. SameSite 설정**

- 크로스사이트 요청 시 쿠키 전송 여부 제어 → CSRF 방어

<br>

**<mark class="yellow">3. 주요 웹 공격과 대응</mark>**

**1\. XSS (Cross-Site Scripting)** [OWASP - XSS](https://owasp.org/www-community/attacks/xss/)  
악성 스크립트를 삽입하는 공격의 한 유형으로, 일반적으로 무해하고 신뢰할 수 있는 웹사이트에 삽입됩니다.

**2\. CSRF (Cross-Site Request Forgery)**  
CSRF 공격은 사용자가 인증된 상태를 악용해, 의도치 않은 상태 변경(POST, DELETE 등)을 수행하게 만드는 기법입니다.

<br>

**<mark class="yellow">4. 쿠키 주요 속성 정리</mark>**

**1\. HttpOnly** [OWASP - HttpOnly](https://owasp.org/www-community/HttpOnly)  
HttpOnly를 지원하는 브라우저가 HttpOnly 플래그가 포함된 쿠키를 감지하고 클라이언트 측 스크립트 코드가 해당 쿠키를 읽으려고 시도하면, 브라우저는 빈 문자열을 반환합니다. 이로 인해 악성 코드(일반적으로 XSS)가 공격자의 웹사이트로 데이터를 전송하는 것을 차단하여 공격이 실패하게 됩니다.

**2\. Secure**  
HTTPS 연결에서만 쿠키가 전송되도록 합니다. 네트워크 스니핑(중간자 공격)으로부터 쿠키를 보호합니다.

**3\. SameSite** [OWASP - SameSite](https://owasp.org/www-community/SameSite)  
SameSite는 브라우저가 이 쿠키를 크로스 사이트 요청과 함께 전송하는 것을 차단합니다. 주요 목표는 크로스 오리진 정보 유출 위험을 완화하는 것입니다.

| 옵션       | 설명                                                                                                                                                                                  |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Strict** | 첫-파티(First-party) 컨텍스트에서만 쿠키 전송. 오직 동일 도메인 네비게이션/요청에만 보내집니다. CSRF 방어에 가장 강력하지만, 링크 클릭 시 세션 쿠키가 빠져 로그인이 풀릴 수 있습니다. |
| **Lax**    | 동일 사이트 요청 및 사용자가 외부에서 해당 사이트로 ‘네비게이션’할 때(예: 링크 클릭) 쿠키를 전송합니다. Strict가 너무 제한적일 때 사용합니다.                                         |
| **None**   | 크로스사이트(Third-party) 컨텍스트에서도 쿠키 전송을 허용합니다. 이 경우 Secure 속성이 필수입니다.                                                                                    |

<br>

**<mark class="yellow">5. frontend에서 쿠키 처리</mark>**

`credentials: "include"`를 추가해주면 자동으로 쿠키를 함께 백엔드 서버로 전달합니다.

```ts
const response = await fetch(`${serverUrl}${API_ENDPOINTS.REFRESH}`, {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
  },
  credentials: "include",
});
```

<br>

| 값              | 설명                                                                     |
| --------------- | ------------------------------------------------------------------------ |
| **omit**        | 어떠한 경우에도 쿠키나 인증 헤더를 전송하지 않습니다.                    |
| **same-origin** | 같은 출처(origin) 요청에 한해서만 쿠키를 전송합니다.                     |
| **include**     | 크로스-오리진(cross-origin) 요청을 포함한 모든 요청에 쿠키를 전송합니다. |

<br>

※ 주의할 점

- 서버도 CORS 설정에서 `Access-Control-Allow-Credentials: true`를 허용해야 합니다.
- `include`를 사용할 때는 응답 헤더에 `Access-Control-Allow-Origin`을 `*` 대신 **정확한 도메인**으로 설정해야 합니다.

<br>
<br>

## <mark class="pink">🔥Refresh Token Rotation (25.04.07)</mark>

Refresh Token 만료 1분 전에 사용자가 서비스에 다시 접속했을 때 1분 뒤에 재로그인을 유도하는 것이 맞는 흐름일까요?

또 Refresh Token을 탈취했을 때 이것으로 Access Token을 계속 발급받아 기존 유저처럼 사용할 수 있는데 보안상 괜찮을까요?

이를 해결하기 위해 **Refresh Token Rotation**을 적용하였습니다.

<br>

**<mark class="yellow">Refresh Token Rotation란?</mark>**

Access Token이 만료될 때마다 기존 Refresh Token을 이용하여 Access Token을 재발급해주는 것 뿐만 아니라, 이후에 새로운 Refresh Token을 발급하여 쿠키에 저장합니다.

위 과정을 통해 Refresh Token 만료 이전에 사용자가 서비스에 다시 접속했다면 Refresh Token 만료 기한이 초기화되어 그 기간동안 재로그인 없이 서비스를 이용할 수 있습니다.

또 Refresh Token 탈취시에도 기존 사용자의 Access Token이 만료되고 나면 유효하지 않은 토큰이 됩니다.

✅ 실제로 Auth 에서도 제안하는 방식입니다. [Refresh Token Rotation](https://auth0.com/docs/secure/tokens/refresh-tokens/refresh-token-rotation)

<br>
<br>

**참고**

[Access Token과 Refresh Token이란 무엇이고 왜 필요할까?](https://velog.io/@chuu1019/Access-Token%EA%B3%BC-Refresh-Token%EC%9D%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B4%EA%B3%A0-%EC%99%9C-%ED%95%84%EC%9A%94%ED%95%A0%EA%B9%8C)
