---
layout: single
title: "[React]react-query 라이브러리"
categories: react
toc: true
toc_sticky: true
---

# react-query 사용해서 api 연결하기

## react-query란?

- React Query는 React 애플리케이션에서 데이터를 관리하고 상태를 처리하기 위한 JavaScript 라이브러리이다.
- 이 라이브러리는 API 호출, 데이터 캐싱, 상태 관리, 그리고 리액티브한 UI 업데이트를 간편하게 처리할 수 있도록 도와준다.
- React Query는 주로 웹 애플리케이션에서 서버에서 데이터를 가져오거나 업데이트할 때 사용되며, 컴포넌트 간 데이터 공유 및 상태 관리를 효율적으로 처리할 수 있다.

```
npm install react-query
```

## 폴더 구조

<div class="blue-box">
  <p>
    <div>index.tsx 파일</div>
    <div>React Query를 애플리케이션에 통합하고 데이터 요청 및 상태 관리를 수행할 수 있게 한다.</div>
  </p>
  <p>
    <div>apis 폴더</div>
    <div>1. index.ts: 사용자 정보를 활용하여 API 요청 헤더에 인증 토큰을 추가하는 Axios 인터셉터 코드</div>
    <div>2. queryKey.ts: 데이터베이스 또는 API에서 데이터를 요청하고 식별하는 데 사용되는 문자열 상수 보관 파일</div>
    <div>3. calendar.ts: 달력과 관련된 api가 모아져 있는 파일로 반환값을 return</div>
  </p>
  <p>
    <div>pages 폴더</div>
    <div>Calendar.tsx: react-query 사용해서 API 호출, 데이터 캐싱, 상태 관리하는 컴포넌트</div>
  </p>
</div>

```
src/
├── apis/
│   ├── index.ts
│   ├── queryKey.ts
│   └── calendar.ts
├── pages/
│   └── main/
│       └── calendar/
│           └── Calendar.tsx
└── index.tsx
```

## index.tsx

> react-query 세팅

<div class="blue-box">
  <p>
    1. QueryClient: React Query의 핵심이며 데이터 요청과 상태 관리를 처리하는 객체이다. 이 객체를 사용하여 데이터를 가져오고 캐싱하며 상태를 관리한다.
  </p>
  <p>
    2. QueryClientProvider: React Query의 컨텍스트를 제공하고 애플리케이션에서 QueryClient를 사용할 수 있도록 한다. 이 컴포넌트는 React 애플리케이션의 상위 수준에서 사용되며, React Query의 다른 컴포넌트 및 hook들이 QueryClient에 액세스할 수 있게 해준다.
  </p> 
  <p>
    <div>QueryClient 객체 생성해준다음</div>
    <div>QueryClientProvider로 App을 감싸주면 react-query 세팅이 완료된다.</div>
  </p>
</div>

```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import { ThemeProvider } from 'styled-components';
import { QueryClient, QueryClientProvider } from 'react-query';

const queryClient = new QueryClient();
const root = ReactDOM.createRoot(document.getElementById('root') as HTMLElement);

root.render(
  <React.StrictMode>
    <ThemeProvider theme={theme}>
      <QueryClientProvider client={queryClient}>
        <App />
      </QueryClientProvider>
    </ThemeProvider>
  </React.StrictMode>,
);
```

## apis 폴더

### 1. index.ts

> 인스턴스 생성

<div class="blue-box">
  <p>
    1. axios.create로 Axios 인스턴스를 생성한다. 이 인스턴스는 특정 baseURL을 기반으로 HTTP 요청을 보낼 수 있게 한다. withCredentials 옵션은 요청에 쿠키를 포함하도록 허용한다.
  </p>
  <p>
    2. config는 Axios가 HTTP 요청을 만들 때 사용하는 설정(configuration) 객체이다.
  </p>
  <p>
    <div>3. goHigerApi.interceptors.request.use를 사용하여 사용자 정보(액세스 토큰)를 요청 헤더에 추가한다.</div>
    <div>로그인을 하여 accessToken이 있다면 config.headers.Authorization에 추가해주고 로그인 전이라면 Authorization이 없는 config를 return 해준다.</div>
  </p>
</div>

```jsx
import axios from 'axios';

interface IUserInfo {
  accessToken: string;
  expireDate: string;
  role: string;
}

export const goHigerApi = axios.create({
  baseURL: process.env.REACT_APP_BASE_URL,
  withCredentials: true,
});

goHigerApi.interceptors.request.use(
  async (config: any) => {
    try {
      const userInfoString: string = localStorage.getItem('userInfo') as string;
      const userInfoJson: IUserInfo = JSON.parse(userInfoString);
      const accessToken = userInfoJson.accessToken;

      if (accessToken) config.headers.Authorization = 'Bearer ' + accessToken;
    } catch {
      return config;
    }
    return config;
  },
  error => {
    Promise.reject(error);
  },
);
```

### 2. queryKey.ts

> 쿼리키 관리

<div class="blue-box">
  <div>
    queryKey는 쿼리 결과를 캐싱하고 관리하는 데 사용된다.
  </div>
</div>

```jsx
export const queryKey = {
  CALENDARDATA: "calendarData",
  DETAILDATA: "detailData",
};
```

### 3. calendar.ts

> api 호출 & 결괏값 반환

<div class="blue-box">
  <div>goHigerApi 인스턴스를 import 후에</div>
  <div>메소드에 맞게 api를 호출한 후</div>
  <div>react-query로 바로 사용할 수 있는 데이터를 반환한다.</div>
</div>

```jsx
import { goHigerApi } from "apis";

export const fetchMonthCalendar = async (year: number, month: number) => {
  const { data }: any = await goHigerApi.get(
    `/v1/applications/calendar?year=${year}&month=${month}`
  );
  return data.data;
};

export const fetchDetailCalendar = async (date: string) => {
  const { data }: any = await goHigerApi.get(
    `/v1/applications/processes?date=${date}`
  );
  return data.data;
};
```

## pages 폴더

### Calendar.tsx

<div class="blue-box">
  <p>
    <div>1. import</div>
    <div>• apis/calendar에서 api 호출 함수를 가져온다.</div>
    <div>• react-query에서 useQuery를 import하여 사용한다.</div>
    <div>• apis/queryKey에서 queryKey를 가져온다.</div>
  </p>
  <p>
    <div>2. react-query 문법</div>
    <div>• const { data } = useQuery(쿼리키, 함수);</div>
    <div>• data: calendarData로 하면 calendarData를 변수처럼 사용할 수 있다.</div>
    <div>• 쿼리키를 [queryKey.CALENDARDATA, currentMonth] 이렇게 배열로 지정하면 첫 번째 요소는 쿼리키이고, 두 번째 요소부터는 저 값이 바뀔 때마다 함수를 실행할 수 있다.(useEffect의 의존성 배열과 비슷함)</div>
  </p>
</div>

```jsx
// --- 다른 import 생략 --- //
import { fetchMonthCalendar, fetchDetailCalendar } from "apis/calendar";
import { useQuery } from "react-query";
import { queryKey } from "apis/queryKey";

const Calendar = () => {
  const [currentMonth, setCurrentMonth] = useState(new Date());
  const [selectedDate, setSelectedDate] = useState(new Date());

  //api 연결
  const { data: calendarData } = useQuery(
    [queryKey.CALENDARDATA, currentMonth],
    () =>
      fetchMonthCalendar(
        parseInt(format(currentMonth, "yyyy")),
        parseInt(format(currentMonth, "MM"))
      )
  );
  const { data: detailData } = useQuery(
    [queryKey.DETAILDATA, selectedDate],
    () => fetchDetailCalendar(format(selectedDate, "yyyy-MM-dd"))
  );

  // --- 중간 생략 --- //

  return (
    <CalendarPage>
      <RenderHeader
        currentMonth={currentMonth}
        prevMonth={prevMonth}
        nextMonth={nextMonth}
      />
      <div className="calendar-detail">
        <CalendarContainer>
          <RenderDays />
          <RenderCells
            currentMonth={currentMonth}
            selectedDate={selectedDate}
            onDateClick={onDateClick}
            calendarData={calendarData}
          />
        </CalendarContainer>

        <DayContainer>
          <RenderDetail
            selectedDate={selectedDate}
            prevDay={prevDay}
            nextDay={nextDay}
            detailData={detailData}
          />
        </DayContainer>
      </div>
    </CalendarPage>
  );
};

export default Calendar;
```
