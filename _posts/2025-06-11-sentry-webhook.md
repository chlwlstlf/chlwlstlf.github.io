---
layout: single
title: "[Next.js] Next15에서 돈 안 쓰고 Sentry 알림 받기"
categories: next
toc: true
toc_sticky: true
---

# Sentry에서 무료로 Discord 알림 보내기

<br>

**관련 PR**  
[[feat] GA, Sentry 추가(#192) #193](https://github.com/ConSeat/frontend/pull/193)  
[[fix] Sentry 디스코드 알림 메시지 변경, 소스맵 추가(#194) #195](https://github.com/ConSeat/frontend/pull/195)  
[[refactor] Sentry 알림 level, 메세지 변경(#196) #197](https://github.com/ConSeat/frontend/pull/197)  
[[fix] 센트리에 오류 두 번 찍는 버그 해결하기(#204) #205](https://github.com/ConSeat/frontend/pull/205)

<br>

## <mark class="pink">🔥Sentry 시작하기</mark>

참고: [Next.js \| Sentry for Next.js](https://docs.sentry.io/platforms/javascript/guides/nextjs/)

**<mark class="yellow">1. 설치</mark>**

```bash
npx @sentry/wizard@latest -i nextjs --saas --org conseat --project javascript-nextjs
```

![Image](https://github.com/user-attachments/assets/d0a9e08e-c6bd-4030-a5ff-535a297eb965)

<br>

**<mark class="yellow">2. 아래 파일들 생김</mark>**

참고 : [Manual Setup \| Sentry for Next.js](https://docs.sentry.io/platforms/javascript/guides/nextjs/manual-setup/)

![Image](https://github.com/user-attachments/assets/45be6901-3d12-493b-b0d6-ec49ccdc928a)

`@sentry/nextjs` 설치하면 **초기 설정 시 자동으로 아래 구조의 파일을 만들어줌**

- `sentry.client.config.js` → 이 파일은 안 만들어줌(내가 모르고 생성한 파일), 예전에 있었지만 `instrumentation-client.js`로 대체 됨
- `sentry.server.config.js` → 서버 (server-side) 에서 init 용
- `sentry.edge.config.js` → Edge Runtime (선택 시) init 용
- `instrumentation-client.js` → **Next.js App Router (app directory)** 용으로 `captureRouterTransitionStart` 사용 시 만들어짐
- `instrumentation.js` → App Router에서 server/edge 별로 register 호출

<br>

**<mark class="yellow">3. 공식문서 보고 다시 세팅</mark>**

`sentry.client.config.js` → 더 이상 쓰지 않아도 됨

> If you previously had a file called sentry.client.config.js, you can safely rename this to instrumentation-client.(js\|ts) for all Next.js versions.
>
> ![Image](https://github.com/user-attachments/assets/687bde90-0d29-4e81-88b2-41f5a033b54a)

-> 공식 가이드 기준으로는  
✅ `instrumentation-client.js`가 클라이언트 초기화도 담당  
✅ `sentry.client.config.js`는 이제 안 써도 됨 (아니면 instrumentation-client.js로 이름 바꾸라고 가이드에 나옴)

<br>

**instrumentation-client.js**

```tsx
import { PUBLIC_ENV } from "./config/env";
import * as Sentry from "@sentry/nextjs";

const isProd = process.env.NODE_ENV === "production";
const isSentryEnabled = isProd && PUBLIC_ENV.sentryDsn;

if (isSentryEnabled) {
  Sentry.init({
    dsn: PUBLIC_ENV.sentryDsn,
    debug: false,
    enabled: true,
    environment: process.env.NODE_ENV,
    release: process.env.NEXT_PUBLIC_SENTRY_RELEASE || `dev-${Date.now()}`, // dev 캐시 방지
    tracesSampleRate: 0.1,
    replaysSessionSampleRate: 0.0,
    replaysOnErrorSampleRate: 0.1,
    sendDefaultPii: false,
  });
} else {
  // 개발환경에서는 init 호출하지 않음 → 느려짐 방지
  console.log("[Sentry] Disabled in local development");
}

export const onRouterTransitionStart = Sentry.captureRouterTransitionStart;
```

<br>

**next.config.ts**

`withSentryConfig`로 감싸준다.

```ts
export default withSentryConfig(pwaConfig(nextConfig), {
  org: "conseat",
  project: "javascript-nextjs",
  authToken: process.env.SENTRY_AUTH_TOKEN, // Sentry source map upload를 위해 필수
  silent: !process.env.CI,
  widenClientFileUpload: true,
  tunnelRoute: "/monitoring",
  disableLogger: true,
  automaticVercelMonitors: true,
});
```

📌 **`SENTRY_AUTH_TOKEN`**  
처음 sentry를 설치했을 때 `.env.sentry-build-plugin`에 자동으로 생성된다.

<br>

**<mark class="yellow">4. DSN 넣기</mark>**

![Image](https://github.com/user-attachments/assets/35911631-a7ad-4c1d-97fb-766484f2d975)

사진 아래쪽 네모 안에 DSN이 있다. DSN을 복사한 후 env에 `NEXT_PUBLIC_SENTRY_DSN`를 넣는다.

<br>
<br>

## <mark class="pink">🔥400 에러가 뜬다!</mark>

![Image](https://github.com/user-attachments/assets/b0cc3e5f-856c-4c63-94e9-a5a50c14a32c)

response 결과

```json
{ "detail": "bad sentry DSN public key", "causes": ["invalid project key"] }
```

<br>

**`Sentry.diagnoseSdkConnectivity()`는 Sentry SDK가 서버와 제대로 연결되어 있는지 확인하기 위한 진단용 메서드이다.**

- 이 메서드는 **Sentry에서 미리 지정한 테스트용 프로젝트(ID: 1337)** 로 요청을 보내 연결 상태를 점검한다.
- 단순히 한 번의 요청만 보내는 것이 아니라, 여러 서버 엔드포인트에 순차적으로 요청하며 각 응답을 확인한다.
- 이 중 일부 요청은 **의도적으로 실패하도록 설계**되어 있어, 에러 응답이 발생해도 문제가 있는 건 아니다. 오히려 이런 실패를 통해 **정상적인 오류 처리가 이뤄지는지 확인하는 것**이 목적이다.

따라서 `diagnoseSdkConnectivity()` 실행 결과에 일부 실패 메시지가 포함되어 있어도,
핵심 엔드포인트(예: 이벤트 전송용 DSN)가 성공한다면 정상적으로 작동 중인 것으로 간주할 수 있다.

<br>
<br>

## <mark class="pink">🔥에러 확인</mark>

You can validate your setup by (re)starting your dev environment (e.g. npm run dev) and visiting "/sentry-example-page”

`/sentry-example-page` 페이지에서 버튼을 눌러 Error를 트리거 한다.

![Image](https://github.com/user-attachments/assets/e1ae1ac9-5d65-4f81-9c8f-ea86a928921b)

<details>
<summary>🔍 Sentry 로그 화면</summary>

<br/>

에러 이름 보임
<img src="https://github.com/user-attachments/assets/025e9574-47a0-49d3-be9d-5a18c9cf48b6" alt="에러 이름 보임" />

소스맵 처리 잘 돼서 원래 코드로 보임
<img src="https://github.com/user-attachments/assets/f34cfcd5-3b4b-4a4c-8f22-493d7f1f2438" alt="소스맵 원본 코드" />

리플레이 볼 수 있음
<img src="https://github.com/user-attachments/assets/ce5d91e9-4441-40fe-99d0-a94b132bfeb9" alt="리플레이 보기 1" />

<img src="https://github.com/user-attachments/assets/98610fb5-506d-46a4-90f3-149a1b6a7fa6" alt="리플레이 보기 2" />

<img src="https://github.com/user-attachments/assets/0997a3cb-0aae-475c-9516-39628d91bc06" alt="리플레이 보기 3" />

Contexts > User: 로그인 후 에러 보내면 사용자 정보(name, email)이 보임
<img src="https://github.com/user-attachments/assets/7b02ac17-681a-48c1-86de-0aafe5cea372" alt="사용자 정보 확인" />

</details>

<br>
<br>

## <mark class="pink">🔥Discord 알림</mark>

참고 : [Sentry 로 에러 추적해보기](https://cabi.oopy.io/59af0a52-9d2b-4655-bfe0-e504cbdfab68), [Webhooks](https://docs.sentry.io/organization/integrations/integration-platform/webhooks/)

**<mark class="yellow">1. 통합 타입 정하기</mark>**

디스코드 알림은 유료 서비스이기 때문에 커스텀 integration를 만들고 Discord 웹훅을 연결해보려고 한다.  
-> 팀 내부에서만 쓰기 때문에 Internal integration 씀

> 참고 : [GitHub - integration-platform-example](https://github.com/getsentry/integration-platform-example)
>
> [Public integrations](https://docs.sentry.io/product/integrations/integration-platform/public-integration) are meant to be accessed by all Sentry Customers, regardless of whether or not they belong to your organization.
>
> If you only wish to provide an application to your team or organization, you should probably develop an [Internal integration](https://docs.sentry.io/product/integrations/integration-platform/internal-integration). These are far easier to get up and running, as they skip the OAuth installation process and become immediately available for webhooks, UI components or API usage.

<br>

Settings -> Integrations -> Internal Integration 선택 후 Next

![Image](https://github.com/user-attachments/assets/0ca2283f-e617-4dae-8d8b-9264fc202eea)

<br>

**<mark class="yellow">2. Discord에서 Webhook URL 발급</mark>**

디스코드에서 원하는 채널 → 채널 설정 → 통합 → Webhook → 새 Webhook 추가 → URL 복사

![Image](https://github.com/user-attachments/assets/2363faa6-2de4-4919-bf42-03c3daa362b2)

<br>

**<mark class="yellow">3. Alert Rule 생성</mark>**

Alerts -> Create Alert

- When: "A new issue is created", "An issue changes state", 등 원하는 조건 선택
- If: environment, tag, level 등을 추가로 필터링 가능 (e.g. environment: production만)
- Then: Send notification via an Integration (내가 만든 Integration 선택)

![Image](https://github.com/user-attachments/assets/4dcf5fbe-e852-4761-83fb-03cce3d0b2be)

<br>
<br>

## <mark class="pink">🔥무료로 Discord 알림 연결</mark>

근데 알림이 안 온다?

**<mark class="yellow">[추측]</mark>**  
Sentry에서 디스코드 연동을 유료로 제공하고 있어서, 디스코드 웹훅으로 바로 연결하는 것을 막은 것 같다.

<br>

**<mark class="yellow">[해결방법]</mark>**

다른 블로그에서 `Cloudflare Workers`를 사용하여 우회하는 것을 보았고, Next의 serverless 기능을 쓰면 될 것 같아 그렇게 우회해보려고 한다.

우회 방법: 프론트 서버에 Webhook endpoint를 만들고 -> 거기서 Discord로 알림을 보내도록 함

<br>

**<mark class="yellow">1. Internal integration의 Webhook URL 변경</mark>**

아까 Discord Webhook URL을 넣어놨었는데 이를 Next 서버의 URL로 변경해준다.

`https://concertseat.site/sentry/webhook`로 설정 -> **Sentry가 이제 이 URL로 POST 요청 보냄**

![Image](https://github.com/user-attachments/assets/ff12b9e5-08a2-4e47-aa8d-643a0838db73)

<div class="blue-box">
  <b>URL 설정</b>
  <br>
  <div>보통은 `주소/api/sentry/webhook`으로 한다.</div>
  <div>우리 서비스는 백엔드 서버 URL이 `도메인 주소/api`이기 때문에 혼란을 방지하기 위해 `/api`를 제거하였다.</div>
</div>

<br>

**<mark class="yellow">2. Next.js에서 `/sentry/webhook` endpoint 만들기</mark>**

`app/sentry/webhook/route.ts` 여기서 POST로 req를 받고, POST로 디스코드에 알림을 보내면 된다.

[Sentry 로그 결과](https://github.com/chlwlstlf/chlwlstlf.github.io/issues/14)

payload 출력 결과를 참고하여 탬플릿을 작성한다.
Discord 알림 메시지에 에러명, API endpoint, 환경 등 유용한 정보를 추가했다.

**app/sentry/webhook/route.ts**

```ts
import { NextRequest } from "next/server";
import { PUBLIC_ENV } from "@/config/env";

export async function POST(req: NextRequest) {
  const payload = await req.json();

  try {
    const event = payload?.data?.event;

    const tags = event?.tags || [];
    const getTag = (key: string) =>
      tags.find(([tagKey]: [string, string]) => tagKey === key)?.[1] || "-";

    const level = event?.level || "error";
    const levelEmojiMap: Record<string, string> = {
      fatal: "💀",
      error: "🚨",
      warning: "⚠️",
      info: "ℹ️",
    };
    const emoji = levelEmojiMap[level] || "🚨";

    const title = event?.title || "알 수 없는 오류";
    const permalink =
      event?.web_url ||
      "https://conseat.sentry.io/issues/?project=4509372106539008";

    const message = event?.exception?.values?.[0]?.value || "메시지 없음";

    const timestamp = event?.datetime
      ? new Date(event.datetime).toLocaleString("ko-KR", {
          timeZone: "Asia/Seoul",
        })
      : "-";

    const url =
      getTag("page_url") ||
      getTag("url") ||
      event?.request?.url ||
      event?.culprit ||
      "-";

    const apiContext = event?.contexts?.api || {};
    const apiEndpoint = getTag("api_endpoint") || apiContext.endpoint || "-";
    const apiMethod = getTag("api_method") || apiContext.method || "-";
    const apiStatus = getTag("api_status") || apiContext.status || "-";
    const isApiError = apiEndpoint !== "-";
    const apiInfo = isApiError
      ? `${apiMethod} ${apiStatus} ${apiEndpoint}`
      : "-";

    const osContext = event?.contexts?.os;
    const osName = osContext?.name || "Unknown OS";
    const osVersion = osContext?.version || "Unknown Version";

    const browserContext = event?.contexts?.browser;
    const browserName = browserContext?.name || "Unknown Browser";
    const browserVersion = browserContext?.version || "Unknown Version";

    // Discord로 보내기
    await fetch(PUBLIC_ENV.discordWebhookUrl!, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        content:
          `${emoji} **${level.toUpperCase()} Sentry 알림**\n` +
          `[${title}](${permalink})\n` +
          `${message}\n` +
          `**발생 시간**\n${timestamp}\n` +
          `**URL**\n${url}\n` +
          `**API URL**\n${apiInfo}\n` +
          `**환경**\n${osName} ${osVersion}, ${browserName} ${browserVersion}`,
      }),
    });

    console.log("✅ Sent alert to Discord");
    return new Response("OK", { status: 200 });
  } catch (error) {
    console.error("❌ Error sending to Discord:", error);
    return new Response("Error", { status: 500 });
  }
}
```

| 이전                                                                                      | 이후                                                                                      |
| ----------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| ![image](https://github.com/user-attachments/assets/4e701021-855a-42d3-9a5d-6b3852fbe660) | ![image](https://github.com/user-attachments/assets/ff10d256-a7d9-450e-a609-94bcc60eb6bf) |
