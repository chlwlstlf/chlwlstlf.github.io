---
layout: single
title: "[우테코] Level3 Refresh Token"
categories: woowacourse
toc: true
toc_sticky: true
---

# Refresh Token으로 Access Token 갱신하기

## <mark class="pink">📌apiClient.ts 코드</mark>

accessToken을 localStorage로 가지고 있었을 때의 코드입니다.

`createRequestInit` 함수는 HTTP 요청을 보낼 때 필요한 RequestInit 객체를 생성하는 역할을 합니다. 이 객체는 fetch API에 요청을 보낼 때 사용됩니다.

`fetchWithErrorHandling` 함수는 실제 HTTP 요청을 보내고 응답을 처리하는 역할을 합니다. 이 함수는 fetch API를 사용하여 네트워크 요청을 보내고, 응답을 확인한 후, 오류가 있으면 적절한 오류를 던집니다. 또한, 오프라인 상태에서 요청을 보내지 않도록 체크합니다.

여기서 refresh token과 Access Token을 추가해보겠습니다.

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

## <mark class="pink">📌JWT란?</mark>

JWT 토큰은 클라이언트와 서버 간의 인증과 정보 교환을 위한 데이터 형식입니다.

Header, Payload, Signature로 구성됩니다.  
Header는 토큰의 타입(JWT)과 암호화 알고리즘, Payload는 유저의 신원 정보나 권한 정보와 같은 데이터를 포함합니다. Signature에는 Header와 Payload를 서버의 비밀키(대칭키) 또는 개인키(비대칭키)로 서명한 값이 담겨있습니다.

<br>
<br>

## <mark class="pink">📌JWT의 문제</mark>

**<mark class="yellow">1. 탈취(Man-in-the-Middle)</mark>**

JWT 토큰이 탈취되면, 공격자는 그 토큰을 사용해 인증된 사용자처럼 서버에 접근할 수 있습니다.
서버는 JWT의 서명을 검증할 수 있지만, 누가 해당 토큰을 사용 중인지는 구분할 수 없습니다. 따라서 탈취된 토큰을 막을 수 없습니다.

<br>

**<mark class="yellow">2. 유효기간 관리의 어려움</mark>**

JWT는 기본적으로 "무상태(Stateless)"이므로, 서버는 토큰을 생성한 이후 만료 전까지 토큰 자체를 철회하거나 무효화할 수 없습니다(단, 예외적으로 Redis와 같은 저장소를 이용해 블랙리스트를 구현할 수 있습니다).

<br>

유효기간이 **짧으면 사용자 경험(UX)**이 저하됩니다(사용자가 로그인을 자주 요구받음).  
유효기간이 **길면 보안 위험**이 커집니다(탈취 시 오랜 시간 동안 유효).

<br>
<br>

## <mark class="pink">📌해결법</mark>

**<mark class="yellow">Access Token과 Refresh Token의 조합</mark>**

JWT 기반 인증 시스템에서 보안과 사용자 경험의 균형을 맞추기 위해 다음 방식을 사용합니다.

<br>

**<mark class="yellow">Access Token</mark>**

유효기간이 짧은 토큰입니다(몇 분~몇 시간).  
클라이언트가 API 요청을 보낼 때마다 이 토큰을 사용하여 인증합니다.

장점: 탈취되더라도 짧은 시간 내에 만료되어 피해를 줄일 수 있습니다.

단점: 유효기간이 짧기 때문에 만료 시 재발급이 필요합니다.

<br>

**<mark class="yellow">Refresh Token</mark>**

유효기간이 긴 토큰입니다(며칠~몇 주).  
Access Token이 만료되었을 때, 클라이언트는 이 토큰을 서버로 보내 새로운 Access Token을 발급받습니다.  
Refresh Token은 보통 서버의 데이터베이스에 저장하거나 관리하여 탈취를 방지합니다.
Refresh Token 만료 시엔 재로그인을 하여 새로운 Refresh Token을 받아야 합니다.

장점: Access Token의 만료를 관리하면서도 사용자가 다시 로그인하지 않아도 됩니다.

단점: Refresh Token이 탈취되면 장기적으로 악용될 가능성이 있습니다.

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

유효하다면, 새로운 Access Token을 생성합니다.

유효하지 않다면, 로그아웃을 강제하거나 재로그인을 요구합니다.

<br>

![1](https://github.com/user-attachments/assets/199ae9f6-f63c-4abe-984b-a1f72293f49b)

<br>
<br>

## <mark class="pink">🔥Refresh Token을 도입한 이유</mark>

프로젝트에 도입한 이유는 사실 다양한 경험을 해보고 싶었기 때문입니다. FE에서 Refresh Token을 구현하는 것이 조금 어렵다는 글을 보았고, 프로젝트에서 그 경험을 하고 싶었습니다.

그 후 많은 서비스에서 Refresh Token을 도입한 이유에 대해 찾아보았을 땐 **보안성과 사용자 경험(UX)**의 균형을 맞추기 위함이라고 하였습니다.

<br>
<br>

## <mark class="pink">🔥토큰 만료 시간</mark>

Access Token은 30분으로 짧게 설정했습니다. 탈취 시 공격자가 사용할 수 있는 기간을 제한하기 위함이었습니다.

Refresh Token은 1주일로 설정했습니다. 도메인이 코드 리뷰인데 이 활동은 일주일을 넘기지 않을 것으로 판단하고 활동을 마친 사이클일 때마다 로그인을 하는 것이 좋을 것 이라고 판단하였습니다.

<div class="blue-box">
  <b>참고</b>
  <div>Google OAuth: Access Token은 1시간, Refresh Token은 최대 6개월.</div>
  <div>GitHub: Access Token은 1시간, Refresh Token은 사용자가 설정한 기간.</div>
</div>

<br>
<br>

## <mark class="pink">🔥처음 작성했던 잘못된 코드</mark>

401 상태가 발생하면 refreshAccessToken 함수에서 새로운 accessToken을 갱신합니다.  
이후 모든 요청은 localStorage에서 갱신된 accessToken을 읽어 사용하기 때문에, 다른 요청들은 401 없이 정상적으로 동작합니다.

하지만 두 개 이상의 작업이 동시에 실행되면서 그 실행 순서나 타이밍에 따라 결과가 달라질 수 있는 `Race Condition` 문제가 발생할 수 있습니다.  
여러 요청이 동시에 발생할 때, Access Token이 localStorage에 저장되기 전에 다른 요청이 401을 반환할 가능성이 있고 여기서 문제가 생길 수 있습니다.

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
      new Promise<string>((resolve, reject) => {
        failedQueue.push({ resolve, reject });
      }).then(async (token) => {
        // 11. processQueue가 호출되어 Promise가 resolve되면 then 블록 실행
        // token은 새 Access Token이며, 이를 사용해 요청을 재실행
        requestInit.headers = {
          ...requestInit.headers,
          Authorization: `Bearer ${token}`,
        };

        response = await fetch(`${serverUrl}${endpoint}`, requestInit);

        if (!response.ok && response.status !== 401) {
          throw new HTTPError(data.message || MESSAGES.ERROR.POST_REFRESH);
        }
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

첫 번째 api 요청 401 상태(109)  
→ refresh  
→ 첫 번째 api 재요청(109)  
→ 대기 중이던 나머지 api 요청(reviewers, reviewees, participants)

![2](https://github.com/user-attachments/assets/82bb3005-4482-40b0-8ca3-0e3ebce08d28)

<br>
<br>

## <mark class="pink">🔥코드 추가 설명</mark>

**<mark class="yellow">failedQueue는 Promise 형태인가?</mark>**

아닙니다. failedQueue는 Promise 객체 자체를 저장하지 않고, resolve와 reject 함수만 저장합니다.

Promise 객체는 해당 함수들을 호출하여 원하는 시점에 완료되거나 에러로 처리되도록 제어할 수 있습니다.

<br>

**<mark class="yellow">Promise의 pending 상태는 고려할 필요가 없는가?</mark>**

Promise는 resolve나 reject가 호출되기 전까지 항상 pending 상태입니다.  
설계된 코드를 보면 processQueue 함수로 대기 중인 요청의 resolve나 reject가 무조건 실행되므로  
Promise의 상태 전환이 명확하여 pending 상태를 직접 관리할 필요가 없습니다.

## 쿠키로 바꿈

> 곧 할 예정

Refresh Token은 HttpOnly 쿠키 또는 안전한 저장소(예: Keychain, Encrypted Storage 등)에 저장해야 합니다.
로컬 스토리지(LocalStorage)는 XSS에 취약하므로 권장되지 않습니다.

Refresh Token은 반드시 안전하게 관리해야 하며, XSS 및 탈취 방지를 위해 HttpOnly 쿠키 사용과 IP/디바이스 검증 등의 보안 조치가 필요합니다.

<br>
<br>

**참고**

[Access Token과 Refresh Token이란 무엇이고 왜 필요할까?](https://velog.io/@chuu1019/Access-Token%EA%B3%BC-Refresh-Token%EC%9D%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B4%EA%B3%A0-%EC%99%9C-%ED%95%84%EC%9A%94%ED%95%A0%EA%B9%8C)
