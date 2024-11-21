---
layout: single
title: "[우테코]Level3 Refresh Token"
categories: woowacourse
toc: true
toc_sticky: true
---

# Refresh Token으로 Access Token 갱신하기

## apiClient.ts 코드

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

## 잘못된 로직

동시 요청 충돌

여러 API 요청이 동시에 실행되고, 이 요청들이 모두 401 상태를 반환하는 경우 각각이 refreshToken을 이용해 별도로 갱신 요청을 보냅니다.
이로 인해 불필요한 갱신 요청이 다수 발생할 수 있고, 경우에 따라 서버에서 갱신 토큰을 무효화하거나 속도 제한(rate limit)에 걸릴 수 있습니다.

새로 갱신된 토큰의 경쟁 조건

한 요청에서 새로 갱신된 토큰이 저장되기 전에 다른 요청에서 여전히 만료된 토큰을 사용하게 되는 경우가 발생할 수 있습니다. 이는 클라이언트에서 갱신된 토큰 관리가 제대로 이루어지지 않음을 나타냅니다.

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
      new AuthorizationError(data.message || MESSAGES.ERROR.POST_REFRESH);
      alert("토큰이 만료되었습니다. 다시 로그인 해주세요!");
      localStorage.clear();
      window.location.href = "/";
    } else {
      throw new HTTPError(data.message || MESSAGES.ERROR.POST_REFRESH);
    }
  } else if (newAccessToken) {
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
    const newAccessToken = await refreshAccessToken();
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

## 개선된 설계의 핵심

단일 갱신 로직

하나의 갱신 작업만 실행되도록 설정하고, 갱신 중인 작업이 완료될 때까지 대기하도록 로직을 개선해야 합니다. 이미 갱신 중이라면 추가로 갱신 요청을 보내지 않도록 해야 합니다. 이를 위해 isRefreshing 플래그와 **failedQueue**를 사용합니다.
