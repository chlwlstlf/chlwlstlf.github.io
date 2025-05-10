---
layout: single
title: "[우테코] Level5 지역 Suspense & 지역 ErrorBoundary"
categories: woowacourse
toc: true
toc_sticky: true
---

# 지역 Suspense와 지역 ErrorBoundary로 배너 계속 보여주기

## <mark class="pink">🔥[이전] 전역 Suspense</mark>

모집 중을 눌렀을 때 전체에 loading이 생기는 게 어색해 보였습니다. 지역 Suspense를 사용해서 방 리스트가 뜨는 곳만 로딩 처리를 하는 것으로 변경하였습니다.

![image (23)](https://github.com/user-attachments/assets/ea2bca2b-867e-4b65-a9b0-82014a9ff618)

<br>
<br>

## <mark class="pink">🔥[변경 후] 지역 Suspense</mark>

모든 방 호출이 useSuspenseQuery로 되어있기 때문에 지역 Suspense를 쉽게 적용할 수 있었습니다.  
방 리스트에 suspense를 감싸주고 fallback UI를 skeleton으로 보여주었습니다.

```tsx
const LocalSuspense = ({ children, fallback }: LocalSuspenseProps) => {
  if (fallback === null) {
    return <Suspense fallback={null}>{children}</Suspense>;
  }

  return (
    <Suspense fallback={<DelaySuspense>{fallback}</DelaySuspense>}>
      {children}
    </Suspense>
  );
};
```

<video controls>
  <source src="https://github.com/user-attachments/assets/b0e0d7ec-55d9-413b-82b0-059a602f9441" type="video/mp4">
</video>

<br>
<br>

## <mark class="pink">🔥[이전] 전역 ErrorBoundary</mark>

무조건 전역 ErrorBoundary로 throw해서 error가 떴을 때 전체 화면이 안 보였습니다.

![image](https://github.com/user-attachments/assets/14fea589-e1a2-4d39-a744-4420d8120ddc)

<br>
<br>

## <mark class="pink">🔥[변경 후] 지역 ErrorBoundary</mark>

ApiError만 잡는 LocalErrorBoundary를 만들고 방 리스트를 감싸서 그 부분에만 fallback UI가 뜨게 했습니다.

```tsx
const LocalErrorBoundary = ({ children, fallback }) => {
  return (
    <ErrorBoundary
      fallback={({ error, resetError }) => {
        if (error instanceof ApiError) return fallback({ error, resetError });
        throw error;
      }}
    >
      {children}
    </ErrorBoundary>
  );
};
```

```tsx
<LocalErrorBoundary
  fallback={({ resetError }) => (
    <RoomListError
      message={MESSAGES.ERROR.GET_PARTICIPATED_ROOM_LIST}
      onRetry={resetError}
    />
  )}
>
  <LocalSuspense fallback={<RoomListLoading />}>
    <ParticipatedRoomList />
  </LocalSuspense>
</LocalErrorBoundary>
```

![image](https://github.com/user-attachments/assets/32ff6313-3057-4c6d-a5d6-81ca0de81d4f)

<br>
<br>

## <mark class="pink">📌useSuspenseQuery</mark>

왜 `useSuspenseQuery`를 쓰면 Suspense와 ErrorBoundary를 쓸 수 있을까요?

`useSuspenseQuery`는 TanStack Query(v4)의 `suspense: true`와 `useQuery`가 합쳐진 v5의 전용 훅입니다.

<br>

**<mark class="yellow">useSuspenseQuery 코드</mark>**

```ts
export function useSuspenseQuery<
  TQueryFnData = unknown,
  TError = DefaultError,
  TData = TQueryFnData,
  TQueryKey extends QueryKey = QueryKey
>(
  options: UseSuspenseQueryOptions<TQueryFnData, TError, TData, TQueryKey>,
  queryClient?: QueryClient
): UseSuspenseQueryResult<TData, TError> {
  if (process.env.NODE_ENV !== "production") {
    if ((options.queryFn as any) === skipToken) {
      console.error("skipToken is not allowed for useSuspenseQuery");
    }
  }

  return useBaseQuery(
    {
      ...options,
      enabled: true, // 항상 쿼리 활성화, 조건부 렌더링 불가능
      suspense: true, // suspense 추가
      throwOnError: defaultThrowOnError,
      placeholderData: undefined,
    },
    QueryObserver,
    queryClient
  ) as UseSuspenseQueryResult<TData, TError>;
}
```

<br>

**<mark class="yellow">동작 원리</mark>**

1\. 로딩 중  
쿼리가 아직 데이터를 불러오는 중에는 내부적으로 해당 Promise를 던져(throw) React가 상위 `<Suspense>`의 fallback을 렌더링하도록 합니다.

2\. 에러 발생 시  
쿼리 함수에서 에러가 발생하면, `throwOnError`가 기본 true여서 에러 객체를 던져 가장 가까운 ErrorBoundary가 잡아줍니다.

3\. 성공 후 렌더링 재개  
데이터가 준비되면 Promise가 이행되고, Suspense 경로가 해제되어 컴포넌트가 정상 렌더링됩니다.

<br>

**<mark class="yellow">직접 겪은 장단점</mark>**

**❤ 장점**

- 로딩을 선언적으로 처리할 수 있습니다. 선언적이라는 말은 `언제`, `어떻게` 보다는 `무엇`에 집중한다는 말입니다. 언제(isLoading), 어떻게(if문)를 생략하고 무엇(fallback UI)에만 집중하여 코드를 짤 수 있습니다.
- 에러도 선언적으로 처리할 수 있습니다. 에러는 `ErrorBoundary`가 담당하므로 useSuspenseQuery로부터 받은 데이터는 항상 null이나 undefined가 아니라고 기대할 수 있습니다. 따라서 `옵셔널 체이닝 ?.`이나 `early return`을 생략하여 코드가 조금 간결해집니다.

<br>

**🤔 단점**

- enabled가 true로 고정되어 있어서 조건부 렌더링을 할 수 없습니다. 알림은 헤더에 있어서 항상 api 요청을 보내지만 로그인이 되어있지 않다면 유효하지 않습니다. 그래서 알림만 `useQuery` + `enabled: !!isLogin`으로 구현하였습니다.
- 무조건 fallback UI를 보여줍니다. useSuspenseQuery를 쓰면서 `<ErrorBoundary>`로 throw를 못 하게 막을 방법은 없습니다. fallback UI와 toast 둘 중에 하나를 보여주고 싶다면 useQuery를 사용해야 합니다.
