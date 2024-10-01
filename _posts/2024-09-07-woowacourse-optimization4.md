---
layout: single
title: "[우테코]Level4 3 같은 건 매번 새로 요청하지 않기"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션1 - cache 사용하기

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>
    <div>Problem: 같은 걸 매번 원본 서버에 요청</div>
  </p>
</div>

## <mark style='background-color: #ffdce0'>🔥CDN 캐시 설정하기</mark>

**<mark style='background-color: #fff5b1'>목표</mark>**

CDN을 적용하고, 한 번 요청한 리소스는 CDN 캐시에서 불러와야 한다. S3, CloudFront 캐시 설정을 적용한다.

<br>

**<mark style='background-color: #fff5b1'>캐시 정책</mark>**

![100](https://github.com/user-attachments/assets/aeafdefb-e023-4415-b0cb-c0d3fe961c8b)

콘텐츠가 어디에서, 어떻게 캐시될지를 정의하는 규칙이다. 주로 CloudFront와 같은 CDN(콘텐츠 전송 네트워크)이나 브라우저에서 어떤 조건 하에 데이터를 캐시할지 관리한다.

CloudFront 캐시는 Recommended for S3로 하였다. S3에 원본을 저장하고 있기 때문에 아래 캐시 정책을 사용하였다. 이 캐시 정책은 Gzip 및 Brotli 압축을 활성화하여 전송되는 데이터의 크기를 줄여준다.

<br>

**<mark style='background-color: #fff5b1'>Cache-Control 주요 설정</mark>**

1\. max-age

- 브라우저나 CDN에서 콘텐츠를 최대 몇 초 동안 캐시할지를 설정한다.
- 정적 콘텐츠(예: 이미지, CSS, JS 파일)처럼 자주 변경되지 않는 콘텐츠에 적용하여 성능을 최적화한다. 1시간 동안 동일한 파일을 서버에서 다시 불러오는 대신 캐시에서 제공한다.

---

2\. s-maxage

- CDN(Shared Cache)에서만 콘텐츠를 최대 몇 초 동안 캐시할지를 설정한다. 이 값은 브라우저 캐시와는 별도로 공유 캐시(예: CloudFront)에만 적용된다.
- 브라우저보다 CDN에서 더 오랫동안 캐시하도록 설정하여, 서버로의 요청을 줄이고 전송 성능을 높일 수 있다. 이 옵션은 동적 콘텐츠를 캐싱할 때 유용하다.

---

3\. no-cache

- 아예 캐시하지 않는다. 브라우저와 CDN 모두 콘텐츠를 저장하지 않고, 매번 서버에서 새로 요청한다.
- 민감한 정보(예: 금융 데이터, 사용자 개인정보)가 포함된 콘텐츠에 사용하여, 캐시를 통해 데이터가 유출되지 않도록 방지한다.

---

4\. private

- 브라우저 캐시에만 저장할 수 있으며, 공유 캐시(CDN 등)에서는 캐시되지 않는다.
- 사용자와 관련된 개인 데이터를 포함하는 콘텐츠에 사용된다. 예를 들어, 사용자 프로필 페이지나 대시보드처럼 개별 사용자만 보아야 할 콘텐츠에 적합하다.

---

5\. public

- 콘텐츠를 브라우저 및 CDN 모두 캐시할 수 있다.
- 자주 변경되지 않는 정적 콘텐츠(이미지, CSS, JS)에 주로 사용되며, 모든 캐시 계층에서 재사용할 수 있도록 한다.

<br>

**<mark style='background-color: #fff5b1'>Cache-Control 사용 예시</mark>**

1\. html

```http
Cache-Control: no-cache
```

캐시는 허용되지만, 브라우저가 콘텐츠를 다시 사용할 때마다 서버에서 유효성을 확인해야 한다. HTML 파일이 항상 최신 정보를 제공해야 할 때 유용하다.

---

2\. 정적 콘텐츠 최적화 (이미지, CSS, JS)

```http
Cache-Control: public, max-age=31536000
```

CDN과 브라우저가 모두 이 콘텐츠를 1년(31536000초) 동안 캐시할 수 있다. 이미지나 자바스크립트 파일 등은 자주 변경되지 않기 때문에 긴 캐시 기간을 설정하여 성능을 최적화할 수 있다. content hash 혹은 version 정보 경로에 포함한다.

---

3\. 동적 콘텐츠 최적화 (API 응답, 사용자 대시보드)

```http
Cache-Control: private, no-cache
```

브라우저에서는 캐시를 사용할 수 있지만, 매번 서버에서 데이터 유효성 검사를 해야 한다. 실시간으로 변화하는 데이터(예: API 응답 데이터)와 관련이 깊다.

---

4\. 민감한 데이터 처리

```http
Cache-Control: no-store
```

캐시되지 않도록 설정하며, 특히 개인정보나 결제 정보 같은 민감한 데이터에 사용된다.

<br>
<br>

## <mark style='background-color: #ffdce0'>🔥API 요청 캐싱하기</mark>

**<mark style='background-color: #fff5b1'>목표</mark>**

GIPHY의 trending API를 Search 페이지에 들어올 때마다 새로 요청하지 않아야 한다. '검색'을 더 주요 기능으로 취급하여, trending 정보가 '실시간으로' 업데이트될 필요는 없다고 가정한다.

<br>

**<mark style='background-color: #fff5b1'>CacheStorage</mark>**

[CacheStorage MDN](https://developer.mozilla.org/en-US/docs/Web/API/CacheStorage)

CacheStorage는 HTTP 응답을 캐싱하도록 설계되어 있어, 외부 API로부터 가져온 데이터를 저장하기에 적합하다. localStorage나 sessionStorage는 단순한 키-값 형태로 데이터를 저장하지만, CacheStorage는 응답 객체 전체(헤더, 상태 코드 포함)를 저장할 수 있기 때문에 CacheStorage를 사용하였다.

1\. 사용자가 매번 접근해야하는 데이터가 있다.  
2\. 그 데이터는 `fetch`를 통해 가져오면 항상 3초가 걸린다고 가정한다.  
3\. `fetch`를 하고 브라우저의 `CacheStorage`를 통해 받아온 데이터를 저장한다.  
4\. 추후에 같은 데이터를 받아오게 된다면, `CacheStorage`의 데이터를 가져온다.

<br>

**<mark style='background-color: #fff5b1'>Cache API</mark>**

- Cache API는 단일 캐시 인스턴스를 조작하는 API이다. 즉, 하나의 캐시에서 항목을 추가, 가져오기, 삭제하는 작업을 할 수 있다.
- cache.add(), cache.match(), cache.put(), cache.delete()

<br>

**<mark style='background-color: #fff5b1'>CacheStorage API</mark>**

- CacheStorage API는 캐시 객체들(Cache API로 관리되는 여러 캐시의 집합)을 관리하기 위한 API이다. 즉, 여러 개의 Cache API 인스턴스를 저장하고 관리할 수 있는 상위 API이다.
- caches.open(), caches.match(), caches.has(), caches.delete()

<br>

**<mark style='background-color: #fff5b1'>코드</mark>**

- Cache, Cache Storages는 Promise를 반환한다.
- caches.match()는 request와 일치하는 response를 반환해준다. 만약 일치하는 반환값이 없다면 undefined를 반환한다. (promise resolves with undefined)
- 12시간 마다 데이터를 다시 불러오기 위해 데이터를 요청할 때 그 시각도 함께 CacheStorage에 저장하였다.

```ts
const CACHE_EXPIRATION_TIME = 12 * 60 * 60 * 1000; // 12시간

const getTrending: async function (): Promise<GifImageModel[]> {
  const url = apiClient.appendSearchParams(new URL(`${BASE_URL}/trending`), {
    api_key: API_KEY,
    limit: `${DEFAULT_FETCH_COUNT}`,
    rating: 'g'
  });

  try {
    // 1. 캐시 스토리지를 열고 검색 결과 확인
    const cacheStorage = await caches.open('trending');
    const cachedResponse = await cacheStorage.match(url);

    // 2. 캐시된 응답이 있는 경우 타임스탬프를 확인
    if (cachedResponse) {
      const cachedTimestamp = await cacheStorage.match(url + '-timestamp');

      if (cachedTimestamp) {
        const timestamp = await cachedTimestamp.json(); //1725600382694
        const now = Date.now();

        // 3. 만료 시간이 지나지 않았다면 캐시된 응답을 반환
        if (now - timestamp < CACHE_EXPIRATION_TIME) {
          const gifs: GifsResult = await cachedResponse.json();
          return convertResponseToModel(gifs.data);
        }
      }
    }

    // 4. 캐시된 응답이 없거나 만료 시간이 지난 경우 네트워크 요청
    const response = await fetch(url.toString());

    if (response.ok) {
      // 5. 네트워크 요청 성공 시, 응답과 타임스탬프를 캐시에 저장
      await cacheStorage.put(url, response.clone());
      await cacheStorage.put(url + '-timestamp', new Response(JSON.stringify(Date.now())));

      const gifs: GifsResult = await response.json();
      return convertResponseToModel(gifs.data);
    } else {
      throw new Error('네트워크 요청 실패!');
    }
  } catch (e) {
    console.error('캐시 또는 네트워크 요청 중 오류 발생:', e);
    return [];
  }
}
```

<br>

**<mark style='background-color: #fff5b1'>결과</mark>**

`CACHE_EXPIRATION_TIME`를 30000(30초)으로 두고 확인한 결과

1\. 캐시된 응답이 없으므로 api를 요청한다.

![102](https://github.com/user-attachments/assets/cfc06b2b-734d-44ae-9bdb-91d265205059)

<br>

2\. 30초가 지나지 않았을 땐 api를 다시 요청하지 않는다.

![103](https://github.com/user-attachments/assets/42d267af-c2a8-45f1-a887-9a5c593b3911)

<br>

3\. 30초가 지났을 땐 다시 api를 요청한다.

![104](https://github.com/user-attachments/assets/6228e0b7-aed7-4579-93a6-5690cb85e9e3)
