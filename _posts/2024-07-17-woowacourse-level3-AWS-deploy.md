---
layout: single
title: "[우테코]Level3 AWS로 프론트 서버 배포하기"
categories: woowacourse
toc: true
toc_sticky: true
---

# S3+CloudFront로 프론트 서버 배포하기

## <mark class="pink">📌S3란?</mark>

- S3 AWS의 가장 초기 서비스 중 하나로, AWS에서 제공하는 스토리지 서비스입니다.
- React같이 SPA로 된 프로젝트의 경우는 자바스크립트만 동작하면 되기 때문에 서버가 필요 없습니다.
- 이런 경우, S3 스토리지에 올리면 서버없이 간단하게 프론트 앱을 런칭할 수 있습니다.

<br>
<br>

## <mark class="pink">📌객체 & 버킷</mark>

- S3에 저장된 데이터 하나 하나를 객체라고 명명하는데, 저장된 파일이라고 생각하면 됩니다.
- 객체가 파일이라면 버킷은 연관된 객체들을 그룹핑한 최상위 디렉토리라고 할 수 있습니다.
- 버킷 단위로 지역(region)을 지정할 수 있고, 또 버킷에 포함된 모든 객체에 대해서 일괄적으로 인증과 접속 제한을 걸 수 있습니다.

<br>
<br>

## <mark class="pink">🔥1. S3 버킷 만들기</mark>

1\. 버킷 이름에 `-` 외에 다른 특수 기호가 들어가면 안 됩니다. [버킷 이름 지정 규칙 보기](https://docs.aws.amazon.com/console/s3/bucket-naming)

![image (27)](https://github.com/user-attachments/assets/7f9a7646-801e-4e16-b295-5dec87370a7d)

2\. AWS 리전은 대한민국(서울)으로 지정합니다. 오른쪽 상단 드롭다운으로 지정할 수 있습니다.

3\. S3가 호스팅 역할을 하길 원하면 차단을 해제해야 합니다. 지금은 Cloudfront와 연결하여 Cloudfront가 호스팅 역할을 하길 원하기 때문에 차단하였습니다.

![image (23)](https://github.com/user-attachments/assets/d66cdab3-4502-498c-aa8b-4a4e293ce4f2)

![image (25)](https://github.com/user-attachments/assets/5784cdfd-ef9e-40ca-8215-d0a18b08eef7)

![image (26)](https://github.com/user-attachments/assets/814bff51-bae7-49de-ada9-a585bd6cdac9)

<br>
<br>

## <mark class="pink">🔥2. 버킷에 빌드 파일 업로드하기</mark>

1\. 생성된 버킷을 찾아 들어갑니다.

2\. 터미널 창에 아래 코드를 실행한 후 생성된 빌드 파일을 올립니다.

```bash
npm run build
```

> 💡 주의 1. dist 폴더를 올리는 게 아니라 dist 폴더 안의 **파일**을 업로드해야 합니다.  
> 💡 주의 2. public 폴더 안의 내용은 빌드가 되지 않으므로 public 폴더 안의 파일도 같이 업로드 합니다.  
> 💡 주의 3. public과 dist 폴더 안에 index.html이 있습니다. 유효한 파일은 dist에 있는 것이므로 public의 index.html말고 dist의 index.html만 올리면 됩니다.

<br>
<br>

## <mark class="pink">🔥5. 정적 웹사이트 호스팅 설정하기</mark>

- 속성 > ARN 복사
- 정적 웹 사이트 호스팅 > 편집
- 아래처럼 세팅

![2](https://github.com/user-attachments/assets/38dceaba-82b7-4bb7-9919-78582d3a0008)

<br>
<br>

## <mark class="pink">📌CloudFront란?</mark>

**1\. 설명**

- 개발자 친화적 환경에서 짧은 지연 시간과 빠른 전송 속도로 데이터, 동영상, 애플리케이션 및 API를 전세계 고객에게 안전하게 전송하는 고속 콘텐츠 전송 네트워크(CDN) 서비스입니다.
- 기본 보안 기능(Anti-DDoS)도 제공합니다.

![3](https://github.com/user-attachments/assets/95079232-978c-42fe-959c-bc66abd1aac4)

<br>

**2\. 장점**

- AWS 네트워크를 사용하면 사용자의 요청이 반드시 통과해야 하는 네트워크의 수가 줄어들어 성능이 향상합니다.
- 파일의 첫 바이트를 로드하는 데 걸리는 지연 시간이 줄어들고 데이터 전송 속도가 빨라집니다.
- 객체의 사본이 전 세계 여러 엣지 로케이션에 유지(캐시)되므로 안정성과 가용성이 향상합니다.
- 오리진 서버에 대한 종단 간 연결의 보안이 보장됩니다.
- 서명된 URL 및 쿠키 사용 옵션으로 자체 사용자 지정 오리진에서 프라이빗 콘텐츠를 제공하도록 할 수 있습니다.

<br>
<br>

## <mark class="pink">📌CDN이란?</mark>

**1\. 설명**

- 콘텐츠를 효율적으로 전달하기 위해 여러 노드를 가진 네트워크에 저장하여 제공하는 시스템입니다.
- 인터넷 서비스 제공자(ISP)에 직접 연결되어 데이터를 전송하므로, 콘텐츠 병목을 피할 수 있습니다.

<br>

**2\. 특징**

- 콘텐츠를 본래 서버에서 받아와 캐싱합니다.
- 해당 콘텐츠에 대한 요청이 들어오면 캐싱해 둔 콘텐츠를 제공합니다.
- 서버의 요청이 필요없기 때문에 서버의 부하를 낮추는 효과가 있습니다.

<br>
<br>

## <mark class="pink">📌엣지 로케이션이란?</mark>

- 컨텐츠가 캐싱되고 유저에게 제공되는 지점입니다.
- CloudFront 서비스는 엣지 로케이션을 통해 콘텐츠를 제공합니다.
- 콘텐츠가 엣지 로케이션에 없는 경우, 콘텐츠의 최종 버전에 대한 소스로 지정된 오리진(S3 버킷, HTTP 서버 등)에서 콘텐츠를 검색한 후 근원에서 제공받아 전달합니다.
- 콘텐츠가 엣지 로케이션에 있는 경우 바로 전달합니다.

<br>
<br>

## <mark class="pink">🔥1. CloudFront에 S3 연결하기</mark>

**1\. 원본 세팅하기**

- Origin domain

  콘텐츠의 최종 버전이 있는 곳입니다. S3에 프론트 빌드 파일을 넣어놨으니 그 버킷을 선택하면 됩니다.

- Origin path - optional

  버킷 안에 폴더가 여러개일 때 본인의 폴더를 적으면 됩니다. `/폴더이름` 형식입니다.

- 이름

  `Origin domain`을 선택하면 자동으로 이름이 입력됩니다.

- 원본 액세스

  `원본 액세스 제어 설정`을 하면 S3 버킷에 대한 접근을 CloudFront로 제한할 수 있습니다. 버킷의 콘텐츠는 CloudFront를 통해서만 접근할 수 있으며, 직접적인 퍼블릭 액세스는 차단됩니다.

![8](https://github.com/user-attachments/assets/75592ec5-e164-4c5a-9c67-7939dcce778b)

<br>

**2\. 기본 캐시 동작 세팅하기**

**기본**

- 경로 패턴

  특정 경로에 대한 캐시 동작을 정의합니다. 모든 경로에 적용하고 싶어서 `기본값(*)`으로 두었습니다.

- 자동으로 객체 압축

  객체를 gzip 형식으로 자동 압축하여 전송합니다. 주로 CSS, JavaScript, HTML 등에 유용하며, 대역폭을 절약하고 페이지 로드 속도를 향상시킵니다.

**뷰어**

- 뷰어 프로토콜 정책

  HTTP 요청을 HTTPS로 리다이렉션 요청하고 싶어서 `Redirect HTTP to HTTPS`을 선택했습니다.

- 캐시 키 및 원본 요청

  미션에서 `Legacy cache settings`로 사용을 제한하고 있기 때문에 이를 사용하였습니다. 이 옵션은 기존의 캐시 설정 방식을 유지하는 방식입니다. `Cache policy and origin request policy`를 선택하면 최신 기능과 최적화를 활용할 수 있습니다.

![6](https://github.com/user-attachments/assets/a78be3b3-c49a-4a20-a0ac-cd75cef8ab54)

<br>

**웹 애플리케이션 방화벽(WAF)**

- 보안 보호 비활성화를 하였습니다.

<br>

**설정**

- 가격 분류

  모든 곳에서 사용해야하기 때문에 `모든 엣지 로케이션에서 사용`합니다.

- 대체 도메인 이름

  도메인을 구매하지 않았기 때문에 사용하지 않았습니다.

- 지원되는 HTTP 버전

  HTTP/3은 최신 HTTP 프로토콜로 2와 3을 둘다 활성화하였습니다.

- 기본값 루트 객체 - 선택 사항

  React는 기본적으로 build를 하면 index.html이 루트 파일이 되므로 `index.html`을 입력해줍니다.

- 설명

  프로젝트명을 입력하였습니다.

![7](https://github.com/user-attachments/assets/8cb03b81-0b95-4f7a-b82d-58b1354e867f)

<br>
<br>

## <mark class="pink">🔥2. S3와 연결하기</mark>

S3와 Cloudfront를 연결하여면 정책을 작성해야 합니다.

`Cloudfront > 원본 > 편집`으로 들어가면 정책을 복사할 수 있습니다. 정책은 JSON 형태입니다.
![image (28)](https://github.com/user-attachments/assets/ed37843a-529f-4977-b372-4ed3981cf009)

그 후 연결한 버킷에 가서 `권한 > 버킷 정책`에 복사한 정책을 붙여 넣으면 됩니다.
![image (29)](https://github.com/user-attachments/assets/36f9b2c2-7602-49ba-b973-512ae4b8c2fb)

## <mark class="pink">🔥3. 오류 응답 생성</mark>

S3와 Cloudfront 연결 후 403 error, Access Denied 에러 메세지가 뜨게 되었습니다. S3에 SPA(React)를 이용하여 구성을 하면 Redirect가 발생하여 403/404와 같은 Access Denied가 발생하게 된 것입니다.

cloudfront에서 아래와 같이 `사용자 정의 오류 응답 생성`을 하면 됩니다.

![100](https://github.com/user-attachments/assets/59422d83-5e22-4aec-94ad-bbda77bdc3aa)

<br>
<br>

## <mark class="pink">🔥4. 무효화하기</mark>

Cloudfront는 기본적으로 24시간동안(기본 TTL) 오리진의 응답을 캐시합니다. 이 동안에 엣지 로케이션에 요청이 오는 경우에는 캐시된 응답을 사용합니다. 따라서 S3 버킷에 새로운 객체를 업로드했을 때 무효화를 해줘야 변경된 페이지가 보입니다.

![101](https://github.com/user-attachments/assets/b62fd543-aabf-407f-bc99-ab2f8b502fd5)

![102](https://github.com/user-attachments/assets/2e18b776-fa5e-4bbe-baab-d2d50782c86b)

<br>

참고자료

[https://velog.io/@crab4862/AWS-S3로-프론트엔드정적-웹사이트-배포하기](https://velog.io/@crab4862/AWS-S3%EB%A1%9C-%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%94%EB%93%9C%EC%A0%95%EC%A0%81-%EC%9B%B9%EC%82%AC%EC%9D%B4%ED%8A%B8-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0)

[[AWS] 웹 프론트, S3 저장소를 통해 배포하자](https://jie0025.tistory.com/554)

[[AWS] 📚 CloudFront 개념 원리 & 사용 세팅 💯 정리](https://inpa.tistory.com/entry/AWS-%F0%9F%93%9A-CloudFront-%EA%B0%9C%EB%85%90-%EC%9B%90%EB%A6%AC-%EC%82%AC%EC%9A%A9-%EC%84%B8%ED%8C%85-%F0%9F%92%AF-%EC%A0%95%EB%A6%AC)
