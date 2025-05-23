---
layout: single
title: "[Next.js] Next에서 Tanstack Query 사용하기"
categories: next
toc: true
toc_sticky: true
---

# 통합 API와 Tanstack Query 세팅

<br>

**관련 PR**  
[middleware, logout 기능 추가](https://github.com/ConSeat/frontend/pull/133)  
[리액트 쿼리 초기 설정하기](https://github.com/ConSeat/frontend/pull/61)

<br>

## <mark class="pink">🔥1. 통합 API 구현</mark>

**<mark class="yellow">❌ 문제점</mark>**

서버 전용 라이브러리는 client에 번들링이 되면 안 된다. CSR에서 서버 전용 라이브러리가 포함된 컴포넌트나 함수를 호출하면 에러가 뜬다.

기존에 serverApi와 clientApi를 각각 만들어서 server 컴포넌트에서는 serverApi를, client 컴포넌트에서는 clientApi를 호출했었다. 하지만 이 방법은 휴먼 에러를 발생시킬 수 있고, 모듈화 한다면 매번 파일을 두 개 만들어야 했다.

<br>

**<mark class="yellow">🔥 해결 방법</mark>**

serverApi와 clientApi는 파일 안에서 token을 가져오는 함수다. 이를 universalApi로 합치고 getAccessToken 함수로 token을 가져오는 코드는 분리하였다.

**authUtils.ts**

```ts
export const getAccessToken = async (): Promise<string> => {
  if (typeof window === "undefined") {
    const { auth } = await import("@/auth");
    const session = await auth();
    return session?.accessToken ?? "";
  } else {
    const res = await fetch("/api/auth/session");
    const session = await res.json();
    return session?.accessToken ?? "";
  }
};
```

<br>

**universalApi.ts**

```ts
import { apiService } from "./apiService";
import { getAccessToken } from "@/utils/authUtils";

const api = apiService(getAccessToken);
export default api;
```

<br>

**apiService.ts**

```ts
export const apiService = (getAccessToken: () => Promise<string>) => {
  const token = await getAccessToken();
  const requestInit = createRequestInit(method, headers, body, token, init);
  return await fetchWithToken<T>(method, endpoint, requestInit, errorMessage);
};
```

<br>
<br>

## <mark class="pink">🔥2. QueryClient 설정</mark>

참고 : [Advanced Server Rendering](https://tanstack.com/query/latest/docs/framework/react/guides/advanced-ssr)

**<mark class="yellow">서버 QueryClient</mark>**

서버에서 QueryClient는 요청이 들어올 때마다 **새롭게 생성**된다. 이를 통해 최신 데이터를 페칭하고 클라이언트에 렌더링 된 HTML을 전달할 수 있다. 서버 QueryClient는 요청의 생애 주기에만 존재하며, 다른 요청과 공유되지 않는다.

<br>

**<mark class="yellow">클라이언트 QueryClient</mark>**

클라이언트에서 QueryClient는 한 번 생성된 후 **재사용**된다. 이 방식은 상태 관리 및 데이터 캐싱을 효율적으로 수행하는 데 유리하다.

페이지를 이동할 때마다 기존 클라이언트를 재사용하여 이전에 페칭한 데이터는 캐시에 남아 있다. 동일한 쿼리를 다시 호출할 때 캐시된 데이터를 사용할 수 있어 불필요한 요청을 줄이고, 성능을 개선한다.

<br>

**<mark class="yellow">QueryProvider.tsx</mark>**

공식 문서에는 아래와 같이 SSR을 사용할 때 클라이언트에서 즉시 refetch가 발생하지 않도록 기본 staleTime을 0보다 큰 값으로 설정하라고 한다. 따라서 1분으로 설정하였다.

```
With SSR, we usually want to set some default staleTime
above 0 to avoid refetching immediately on the client
```

```tsx
function makeQueryClient() {
  return new QueryClient({
    defaultOptions: {
      queries: {
        retry: false,
        throwOnError: true,
        staleTime: 60 * 1000, // SSR에서 불러온 정보를 CSR에서 하이드레이션하여 바로 사용하기 위함
        networkMode: "online",
      },
      mutations: {
        networkMode: "always",
      },
    },
    queryCache: new QueryCache({
      onError: (error) => {
        alert(error.message);
      },
    }),
    mutationCache: new MutationCache({
      onError: (error) => {
        alert(error.message);
      },
    }),
  });
}

let browserQueryClient: QueryClient | undefined = undefined;

function getQueryClient() {
  if (isServer) {
    return makeQueryClient();
  } else {
    if (!browserQueryClient) browserQueryClient = makeQueryClient();
    return browserQueryClient; // CSR일 때 매번 makeQueryClient 하는 것 막음
  }
}
```

<br>
<br>

## <mark class="pink">🔥3. Hydration</mark>

**<mark class="yellow">서버 컴포넌트에서 프리페치</mark>**

- `dehydrate(queryClient)` : `QueryClient` 내부의 캐시 상태를 직렬화해서 객체로 반환, 이 객체에서 쿼리키와 데이터 정보가 있다.
- `<HydrationBoundary state={...}>` : 직렬화된 캐시 상태를 자식 컴포넌트에 주입, 클라이언트 쪽에서 `hydrate()`가 자동으로 실행되어 동인한 캐시를 복원해준다.

```tsx
const HomePage = async () => {
  const queryClient = new QueryClient();

  queryClient.prefetchQuery({
    queryKey: stadiumKeys.all,
    queryFn: getStadiumList,
  });

  return (
    <>
      <SecondBackground />
      <HydrationBoundary state={dehydrate(queryClient)}>
        <HomeClient />
      </HydrationBoundary>
    </>
  );
};
```

<br>

**<mark class="yellow">클라이언트에서 훅 사용</mark>**

- `useQuery({ queryKey, queryFn })` : 서버에서 이미 `prefetchQuery`로 채워둔 캐시에 접근한다. staleTime이 1분이기 때문에 캐시가 살아있고, 네트워크 요청 없이 바로 data를 반환한다.

```tsx
const HomeClient = () => {
  // 같은 api 요청
  const { data } = useQuery({
    queryKey: stadiumKeys.all,
    queryFn: getStadiumList,
  });

  return <></>;
};
```

<br>

**<mark class="yellow">createPrefetchedQueryClient</mark>**

매번 해당 코드를 작성하기엔 보일러 플레이트가 많아져 휴먼 에러를 발생시킬 수 있을 거라 생각했다. 그래서 이를 모듈화하였다.

```ts
const queryClient = new QueryClient();

queryClient.prefetchQuery({
  queryKey: stadiumKeys.all,
  queryFn: getStadiumList,
});
```

<br>

**createPrefetchedQueryClient.ts**

`queryKey`와 `queryFn`이 담긴 여러 api 요청 배열을 인자로 보내면 `dehydratedState`를 반환한다.

```ts
import { QueryClient, dehydrate } from "@tanstack/react-query";

export type PrefetchableQuery = {
  queryKey: readonly unknown[];
  queryFn: () => Promise<unknown>;
};

export const createPrefetchedQueryClient = async (
  queries: PrefetchableQuery[]
) => {
  const queryClient = new QueryClient();

  for (const { queryKey, queryFn } of queries) {
    await queryClient.prefetchQuery({ queryKey, queryFn });
  }

  return {
    queryClient,
    dehydratedState: dehydrate(queryClient),
  };
};
```

<br>

**사용 예시**

```tsx
export const stadiumQueries = {
  concerts: (stadiumId: number, query?: string) => ({
    queryKey: stadiumKeys.concerts(stadiumId, query),
    queryFn: () => getStadiumConcerts(stadiumId, query),
  }),
  seats: (stadiumId: number) => ({
    queryKey: stadiumKeys.seats(stadiumId),
    queryFn: () => getStadiumSeats(stadiumId),
    staleTime: Infinity,
  }),
  features: {
    queryKey: stadiumKeys.features(),
    queryFn: getStadiumFeatures,
    staleTime: Infinity,
  },
  obstructions: {
    queryKey: stadiumKeys.obstructions(),
    queryFn: getStadiumObstructions,
    staleTime: Infinity,
  },
};

const ReviewPage = async ({ params }) => {
  const { stadiumId } = await params;

  // 여러 api 프리페칭, dehydratedState 반환
  const { dehydratedState } = await createPrefetchedQueryClient([
    stadiumQueries.concerts(stadiumId),
    stadiumQueries.seats(stadiumId),
    stadiumQueries.features,
    stadiumQueries.obstructions,
  ]);

  return (
    <HydrationBoundary state={dehydratedState}>
      <ReviewContainer stadiumId={Number(stadiumId)} />
    </HydrationBoundary>
  );
};
```

<br>
<br>

## <mark class="pink">🔥마무리</mark>

Next.js에서 리액트 쿼리를 사용하는 방식은 리액트와 많이 다르다. [공식 문서](https://tanstack.com/query/latest/docs/framework/react/guides/advanced-ssr)가 친절하게 설명해주어 잘 따라갈 수 있었다.

다음 글에서는 `getAccessToken`에서 `session` api를 매번 호출했는데 이를 해결한 내용에 대해 다뤄보겠다.
