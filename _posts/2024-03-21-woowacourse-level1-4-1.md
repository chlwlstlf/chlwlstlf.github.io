---
layout: single
title: "[우테코]Level1 AJAX&JSON, RESTful API, Promise&async/await"
categories: woowacourse
toc: true
toc_sticky: true
---

# 미션4 LMS 강의 자료1

## <mark class="pink">📖웹의 동작</mark>

### <mark class="yellow">1. 웹 서버와 웹 클라이언트</mark>

클라이언트-서버 모델 구조는 client가 요청하면 server가 응답해주는 구조이다. 웹서버는 네트워크상에 공개하는 하이퍼텍스트(HTML 형식의 파일)를 쌓고, 웹 클라이언트가 요청하는 HTML 파일을 건네주는 구조로 되어있다.

<br>

**왜 클라이언트와 서버로 나뉘는 것일까?**  
웹에 존재하는 대부분의 콘텐츠는 업데이트가 이루어진다. 그런데 여러곳에 분산돼 있으면 어디에 저장되어 있는지 파악하고 동시에 갱신하는 것이 매우 어렵고 비용이 많이 드는 작업이 된다. 즉 웹 서버와 같이 컴퓨터 하나에 정보를 모아 두는 편이 관리가 수월하다.

또한 웹은 불특정 다수의 사람이 콘텐츠를 자유롭게 열람할 수 있어야 하는데, 사용자가 콘텐츠를 열람하기 위해 그 콘텐츠를 보관하고 처리하는 웹 서버를 직접 조작하는 것은 비현실적인 일이다. 그래서 사용자 앞에 있는 PC를 웹 클라이언트로, 콘텐츠 자원을 관리하고 처리하는 컴퓨터를 웹서버로 만들어 인터넷으로 연결하는 것이다.

<br>

**그 리소스(html 파일)는 어디서 찾아서 주는 것일까? - URL**  
클라이언트(유저)는 "어디 어디에 있는 이 콘텐츠를 읽고 싶다"라고 지정할 방법이 필요하다. 따라서 인터넷 상의 콘텐츠를 고유하게 지정하기 위한 구조가 URL(Uniform Resource Locator)인 것이다. 즉 유저가 웹 브라우저로 특정 사이트에 접속할 때 주소창에 입력하는 문자열이 바로 URL이다. 예를 들어 유저가 은행에서 돈을 찾는 화면을 이용한다면 아래와 같은 방식으로 요청할 수 있다.

![](https://user-images.githubusercontent.com/50367798/60485705-d2803f80-9cd8-11e9-8df4-e20996be695b.png)

![](https://user-images.githubusercontent.com/50367798/60485707-d4e29980-9cd8-11e9-8261-5640fee1c41b.png)

<br>
<br>

### <mark class="yellow">2. HTTP라는 약속</mark>

우리는 URL을 이용함으로써 웹 상에 공개된 다양한 콘텐츠를 얻을 수 있다. 하지만 웹의 구현에는 한 가지 더 넘어야 할 산이 있다. 바로 하이퍼텍스트를 비롯한 콘텐츠를 컴퓨터가 **어떻게** 송수신할지에 대한 것이다.

인터넷에는 다양한 종류의 컴퓨터가 연결돼있는데, 웹 서버와 웹 클라이언트가 통신하려면 어떻게 정보를 주고 받을지에 대한 약속이 필요하다. 이 약속을 `통신 프로토콜` 이라고 한다. 웹의 창시자 버너스 리 박사는 HTML 전송에 적합한 프로토콜을 새로 고안했다. 이것이 현재도 널리 사용되고 있는 HTTP(HyperText Transfer Protocol)이다. HTTP는 다른 프로토콜에 비해 매우 단순해서 간단히 구현할 수 있어 쉽게 널리 이용됐다.

![](https://www3.ntu.edu.sg/home/ehchua/programming/webprogramming/images/HTTP_Steps.png)

<br>
<br>

**1\. URL(Uniform Resource Location)**

![](https://user-images.githubusercontent.com/50367798/60554681-859d7700-9d73-11e9-9859-38b628ca957c.png)

- URI(Uniform Resource Identifier)라고도 한다.
- URI가 URL보다 더 상위 개념이지만 현재 혼용해서 사용하고 있다.
- URL은 스킴(scheme), 호스트명, 경로명으로 구성된다. 위 예에서 스킴은 `http`, 호스트명은 `wooteco.com`, 경로명은 `webcontents/index.html`이 된다.

<br>

**스킴(scheme)**  
스킴은 리소스를 획듣하기 위한 방법을 나타낸다. 웹 애플리케이션에서는 대부분의 경우 HTTP 프로토콜을 사용한다.

- https: 암호화된 http 통신을 나타내는 스킴
- mailto: 이메일의 수취인을 나타내는 스킴
- ftp: FTP 프로토콜을 통한 파일 획득을 나타내는 스킴
- file: 파일 시스템 속의 파일이나 디렉터리를 참조하기 위한 스킴

<br>

**호스트명**  
리소스가 존재하는 호스트(컴퓨터)의 이름을 나타낸다. 인터넷을 비롯한 컴퓨터 네트워크의 세계에서 네트워크에 접속되어 다른 컴퓨터로부터 요구를 받고 처리한 결과를 되돌려주는 컴퓨터를 일반적으로 호스트 컴퓨터라고 한다. 호스트명은 호스트 컴퓨터의 이름을 가리킨다.

<br>

**경로명**  
호스트명에서 지정된 컴퓨터상의 리소스 위치를 나타낸다.

```
www.wootecto.com/webcontents/index.html
```

위 경로에서는 `webcontents` 하위에 있는 index.html이라는 파일을 나타낸다. 이처럼 URL을 이용하여 도메인->컴퓨터->디렉터리->파일명과 같이 계층적으로 리소스의 위치를 지정할 수 있어 인터넷상에서 리소스의 위치를 나타낼 수 있다.

<br>
<br>

**2\. HTTP Request Line**

![](https://www3.ntu.edu.sg/home/ehchua/programming/webprogramming/images/HTTP_RequestMessageExample.png)

<br>

**메서드(method)**  
요청의 종류를 나타낸다. 여기서는 GET, 즉 "URL 에서 지정한 정보를 보내주세요"라는 의미가 된다. 메서드에는 그 밖에도 몇 가지가 정의돼 있는데, 웹 브라우저에서 웹 서버로 송신되는 요청의 대부분은 GET 메서트에 따른 요청이다.

<br>

**URI(Uniform Resource Identifier)**  
GET 메서드는 단순히 "정보를 주세요"라는 의미에 불과하며, URI는 "무엇을 원하는가"를 나타낸다. (URL과 URI는 거의 같은 것으로 생각해도 무방하다.)

<br>

**HTTP 버전**  
버전에 따라 이용할 수 있는 메서드의 종류와 요청 헤더의 종류가 달라지므로 어떤 버전에 따른 요청인지 저장한 것이다. 두번 째 줄 이후의 나머지 부분은 메세지 헤더라고 하며, 요청의 부가적인 정보를 나타낸다.

<br>

**Accept**  
웹 클라이언트가 받을 수 있는 데이터의 종류를 표시한 것이다. 데이터의 종류를 Content-Type이라는 형식으로 표시되며, 클라이언트에서 받을 수 있는 Content-Type을 콤마로 구분해서 지정한다. 이렇게 정보를 표현함으로써 Accept 필드를 참조하면 웹 서버는 불필요한 정보를 송신하지 않아도 될 가능성이 있는 것이다.

<br>

**Accept-Language**  
웹 클라이언트가 받을 수 있는 자연 언어의 종류를 나타낸다. 자연 언어는 사람이 사용하는 언어를 가리킨다. 여기서는 `en-us`로 되어 있으므로 영어를 나타낸다.

<br>

**User-Agent**  
이용 중인 웹 브라우저의 종류와 버전을 나타낸다.

<br>

**Host**  
요청을 보낸 곳의 호스트명과 포트번호를 지정한다.

<br>
<br>

**3\. HTTP response**

![](https://www3.ntu.edu.sg/home/ehchua/programming/webprogramming/images/HTTP_ResponseMessageExample.png)

<br>

**상태라인**  
HTTP 요청과 마찬가지로 HTTP 응답에서도 첫 번째 줄이 가장 중요하며, 이것을 상태 라인이라고 한다. HTTP 상태 라인도 구성은 간단하다. HTTP 버전, 상태 코드, 응답 구문 세 부분으로 나뉜다.

![](https://user-images.githubusercontent.com/50367798/60555129-19704280-9d76-11e9-9c79-8e8b7e384037.png)

HTTP 버전은 요청에서와 마찬가지로 사용하는 프로토콜의 버전을 나타낸다. 중요한 것은 상태 코드로 이 부분을 보면 요청이 성공했는지 실패했는지 쉽게 알 수 있다. 200은 요청이 성공해 정상적인 응답이 돌아왔음을 나타내는 상태 코드이다.

<br>

**[대표적인 HTTP status code]**

<div class="blue-box">
  <b>💡 status code 단위별 의미는?</b>
  <div>2XX: 성공. 클라이언트가 요청한 동작을 수신하여 이해했고 승낙했으며 성공적으로 처리</div>
  <div>3XX: 리다이렉션 완료. 클라이언트는 요청을 마치기 위해 추가 동작이 필요하다.</div>
  <div>4XX: 요청 오류. 클라이언트에 오류가 있다.</div>
  <div>5XX: 서버 오류. 서버가 유효한 요청을 명백하게 수행하지 못했다.</div>
</div>

- 200: OK
- 201: Created
- 302: Found(HTTP 1.0)
- 304: Not Modified
- 401: Unauthorized
- 404: Not Found
- 500: Internal Server Error
- 503: Service Unavailable

<br>

**메세지 헤더**  
상태 라인에서 이어서 나오는 것이 메세지 헤더로 두 번쨰 줄부터 빈 줄까지 계속된다. 메세지 헤더는 HTTP 요청의 메세지 헤더와 같은 형식으로 응답에 관한 부가적인 정보가 들어있다.

<br>

**메세지 본문**  
웹 브라우저는 메세지 본문에 있는 HTML을 해석해 화면에 표시한다. HTML은 텍스트 형식이므로 우리가 읽을 수 있는 형식으로 메세지 본문에 저장돼 있다. 그러나 GIF나 JPEG 형식의 이미지 파일을 요청했을 경우에도 마찬가지로 그 데이터가 메세지 본문에 들어간다.

<br>
<br>

**4\. HTTP method**

![](http://www.aliencoders.org/wp-content/uploads/2016/01/http-header-functions.jpg)

주로 많이 사용하는 메소드는 `GET`과 `POST`이다.

`GET`은 Select적인 성향을 가지고 있다. GET은 서버에서 어떤 데이터를 가져와서 보여준다거나 하는 용도이지 서버의 값이나 상태 등을 바꾸지 않는다. 게시판의 리스트라던지 글 보기 기능 같은 것이 이에 해당한다.

`POST`는 서버의 값이나 상태를 바꾸리 위해서 사용한다. 글쓰기를 하면 글의 내용이 데이터베이스에 저장이 되고 수정을 하며 데이터베이스 값이 수정된다. 이럴 경우에 POST를 사용한다.

<br>
<br>

## <mark class="pink">📖AJAX & JSON</mark>

### <mark class="yellow">1. AJAX</mark>

> Asynchronous JavaScript and XML

`AJAX` 또는 `XMLHttpRequest`는 비동기 방식으로 데이터를 주고받기 위해 개발된 자바스크립트 기술이다.

초기에는 XML/XLST 포맷 형태의 데이터를 주고받다가 최근에는 JSON 형태의 데이터를 주고받는 웹 비동기 기술로 정착되고 있다.

AJAX는 HTML, XML, JSON 등 다양한 데이터를 주고받을 수 있다. AJAX를 위해서 자바스크립트는 XMLHttpRequest 객체를 사용한다. XMLHttpRequest 객체는 자바스크립트의 내장 객체로 비동기 통신 구현을 위한 객체이며, 이 객체는 서버로 요청을 보내고 요청받은 결과를 비동기로 처리한다. 따라서 요청을 보낸 후 사용자는 UI 화면과 상호작용을 하거나 다른 AJAX 요청을 추가로 보낼 수 있다.

서버에서 응답이 오면 AJAX 요청을 보낼 때 등록한 콜백 함수를 통해 결과 데이터를 처리하게 된다.

<br>

### <mark class="yellow">2. JSON</mark>

> JavaScript Object Notation

AJAX로 받아오는 대부분의 데이터는 JSON 포맷이다. 비동기로 받은 데이터가 HTML이면 웹 페이지에 바로 붙여널을 수 있지만, JSON 데이터면 동적으로 HTML 태그들을 생성해 넣어야 한다. 전통적인 웹페이지와 다르게 동적 웹페이지를 생성하는 데 있어 JSON을 사용해야 하는 이유는 JSON으로 전송하는 데이터의 양이 HTML보다 훨씬 적기 떄문에 느린 인터넷 환경, 특히 모바일 환경에서 **더 빠른 페이지 로딩과 페이지 갱신**을 보장할 수 있기 떄문이다.

또한, HTML 데이터를 받아 웹페이지에 붙이는 과정이 적은 자바스크립트 코드만으로 구현이 가능하지만, 내부 과정은 결국 HTML 데이터를 파싱해 DOM 노드를 생성해 붙이는 과정을 거치기 때문에 JSON 데이터를 사용해 동적으로 HTML 페이지를 갱신하는 과정과 차이가 없다.

AJAX로 받아올 수 있는 데이터 포맷에 제약이 있는 것은 아니지만, 웹에서 오고 가는 데이터의 대부분이 JSON으로 표현이 가능하기 때문에 사실상 표준으로 정착해 있다. AJAX 응답으로 받은 JSON 데이터는 단순 문자열이기 때문에 그냥 사용할 수는 없고 데이터 변환을 해야 한다. 응답을 받은 XMLHttpRequest 객체에는 `responseText` 속성이 있고, 이 속성에 응답으로 받은 JSON 텍스트 데이터가 있다. 이 데이터를 JSON 객체로 변환해야 한다.

자바스크립트에는 전역 객체로 JSON 객체가 있으며, JSON 문자열을 객체로 변환해주는 메서드가 제공된다.

```js
const json = JSON.parse(xhr.responseText);
```

`json` 변수는 JSON 객체 참조를 나타내고, 속성을 통해 JSON 값에 접근할 수 있다.

<br>
<br>

## <mark class="pink">📖RESTful API</mark>

`REST(Representational State Transfer)`는 HTTP **URI를 통해 자원을 명시**하고, HTTP Method를 통해 해당 자원에 대한 CRUD Operation을 적용하는 것을 의미한다.

**[REST API 특성]**

- API가 웹 서버를 통해 제공된다.
- `GET/users/[사용자 id]/repositories`처럼 경로에 메서드를 보내 서비스를 얻는다.
- API가 성공했는지 **status code**로 클라이언트에 알려준다.
- URL은 리소스의 위치를 나타낸다.
- 필요에 따라 GET 매개변수, POST의 바디 등 추가 정보를 보낼 수도 있다.
- 서버에서 오는 반환값으로는 **JSON** 또는 XML과 같은 구조와 텍스트나 이미지 데이터 등이 반환되는 경우가 많다.

<br>

**[클라언트 관점]**

- URL은 리소스의 계층을 나타낸 경로로 되어 있다. 명사로만 구성된다.
- 리소스에 대해 HTTP 메서드를 보내 리소스 취득, 갱신, 추가 등의 작업을 한다.
- status code를 보고 요청이 제대로 처리됐는지 판정할 수 있다.
- GET 메서드는 여러번 호출해도 상태를 변경하지 않는다.
- 클라언트 쪽에서 관리해야 할 상태가 존재하지 않고, 매번 요청을 독립적으로 발행할 수 있다.

<br>
<br>

## <mark class="pink">📖Promise & async/await</mark>

### <mark class="yellow">1. Promise 설명</mark>

**[MDN 문서 내용]**  
Promise는 Promise가 생성될 때 꼭 알 수 있지는 않은 값을 위한 대리자로, 비동기 연산이 종료된 이후의 결과값이나 실패 이유를 처리하기 위한 처리기를 연결할 수 있도록 한다.

Promise를 사용하면 비동기 메서드에서 마치 동기 메서드처럼 값을 반환할 수 있다. 다만 최종 결과를 반환하지는 않고, 대신 Promise를 반환해서 **미래의 어떤 시점에 결과를 제공**한다.

미랫값인 Promise는 성공할 수도, 실패할 수도 있습니다.

<br>

### <mark class="yellow">2. 콜백</mark>

Promise가 등장하기 전에 사용하던 방법이다.

**[콜백 지옥]**  
서비스의 로직이 복잡해지고 여러 비동기 동작들이 필요한 시점부터 비동기 함수에서 다른 비동기 함수를 호출하고, 거기서 또 다른 비동기 함수를 호출하는 등의 중첩이 발생했다. 이런 경우를 **콜백 지옥**에 빠졌다고 한다.

```js
callback1(function (value1) {
  callback2(value1, function (value2) {
    callback3(value2, function (value3) {
      callback4(value3, function (value4) {
        callback5(value4, function (value5) {
          // value5를 사용하는 처리
        });
      });
    });
  });
});
```

<br>

**[에러 처리 어려움]**  
try 블록 내에서 setTimeout 함수가 실행되면 1초 후에 콜백 함수가 실행되고 이 콜백 함수는 예외를 발생시킨다. 하지만 이 예외는 catch 블록에서 catch되지 않는다. 이미 try catch 문을 빠져나온 다음에 setTimeout 함수가 1초 뒤에 실행되기 때문이다.

```js
try {
  setTimeout(() => {
    throw new Error("Error!");
  }, 1000);
} catch (e) {
  console.log("에러를 캐치하지 못한다..");
  console.log(e);
}
```

<br>
<br>

### <mark class="yellow">2. Promise</mark>

Promise는 콜백 함수를 인자로 받는 대신에 **성공과 실패에 대응하는 메서드**를 제공한다. 그리고 콜백 함수를 중첩하지 않고, 여러 개의 비동기 동작을 연결할 수 있는 방법을 제공한다.

Promise는 비동기 작업을 전달받아서 응답에 따라 두 가지 메서드 중 하나를 호출하는 객체이다. Promise는 비동기 작업이 성공하거나 충족된 경우 then() 메서드에 결과를 넘겨준다. 비동기 작업에 실패하거나 거부되는 경우에는 catch() 메서드를 호출한다. then()과 catch() 메서드에는 모두 함수를 인자로 전달한다. 이때 두 메서드에 전달되는 함수에는 비동기 작업의 결과인 응답만이 인수로 전달된다.

Promise는 두 개의 인자. **resolve()**와 **reject()**를 전달받는다. resole()는 코드가 정상적으로 동작했을 때 실행된다. resolve()가 호출되면 then() 메서드에 전달된 함수가 실행된다. Promise를 설정할 때 then()과 catch 메서드를 모두 사용할 수 있다. then() 메서드는 성공한 경우를 처리하고, catch() 메서드는 거절된 경우를 처리한다.

```js
new Promise(function (resolve, reject) {
  // ...
})
  .then(resolve())
  .catch(reject());
```

<br>

![](https://techcourse-storage.s3.ap-northeast-2.amazonaws.com/2020-05-14T20%3A11%3A42.267image.png)

then()을 연속으로 사용하면, 여러 개의 중첩된 콜백 함수에 데이터를 전달하는 대신 여러 개의 then() 메서드를 통해 데이터를 아래로 내려줄 수 있다. 그리고 Promise를 반환하므로, 암묵적인 반환을 이용하는 화살표 함수로 모든 코드를 한 줄로 만들 수 있다.

```js
getUserPreferences()
  .then((preference) => getMusic(preference.theme))
  .then((music) => {
    console.log(music.album);
  });
```

여기서 Promise를 연결하는 경우에는 catch() 메서드를 개별적으로 연결할 필요가 없다. catch() 메서드를 하나만 정의해서 Promise가 거절되면 모든 경우를 처리할 수 있다.

이제 비동기 작업을 처리할 수 있는 도구를 다룰 수 있게 되었다. 이런 Promise가 가장 많이 사용되는 상황 중 하나는 바로 API에서 데이터를 가져오는 경우이다. 그렇다면 이 Promise를 활용해 자바스크립트에서 데이터를 가져오는 fetch에 대해 알아보자.

<br>
<br>

### <mark class="yellow">3. fetch</mark>

규모가 있는 자바스크립트 앱을 개발하는 경우에는 필연적으로 API를 다루게 된다. API를 이용하면 페이지가 아닌 **데이터**를 가져올 수 있어서, 화면을 새로 고침하지 않아도 요소를 갱신할 수 있다. 즉 문서를 애플리케이션으로 만들 수 있다.

최근 프론트엔드의 트렌드 중 하나인 단일 페이지 웹앱은 자바스크립트가 인기를 끄는 이유 중 하나이지만, Ajax로 데이터를 가져오는 작업은 오랜 시간 동안 꽤 번거로운 방법이었다. 그래서 오랜시간 개발자들은 복잡도를 낮춘 jQuery와 같은 라이브러리를 사용했다.

<br>

**[GET 요청]**

fetch()를 사용하려면 API 끝점(엔드 포인트)이 필요하다.

요청: GET 요청은 아래와 같이 fetch 인자로 끝점을 넣어 호출하면 된다.

```js
fetch("https://jsonplaceholder.typicode.com/posts/1");
```

응답: 해당 포스트 정보를 받았다.

```
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere repellat provid...",
  "body": "quia et suscipit\nsuscipit rec..."
}
```

<br>

요청을 보내고 나면 fetch()는 응답을 처리하는 Promise를 반환한다.  
이어서 해야 할 작업은 then() 메서드에 응답을 처리하는 콜백함수를 추가하는 것이다. json()이라는 메서드를 호출해 응답객체를 JSON으로 변환할 수 있다.

💡 주의할 점. json() 메서드도 Promise를 반환하기 때문에 then() 메서드를 추가해야 한다. 추가한 then() 메서드에서 콜백에서 파싱된 데이터를 처리할 수 있다.

제목만 가져오기

```js
fetch("https://jsonplaceholder.typicode.com/posts/1")
  .then((data) => {
    return data.json();
  })
  .then((post) => {
    console.log(post.title);
  });
```

<br>

**에러 처리**  
응답 코드가 200에서 299 사이인 경우 true로 설정되는 ok라는 필드가 있다. 이 필드를 이용해서 응답을 확인하고, 오류가 있는 경우 오류를 처리할 수 있다.

```js
fetch("https://jsonplaceholder.typicode.com/posts/1")
  .then((data) => {
    if (!data.ok) {
      throw new Error(data.status);
    }
    return data.json();
  })
  .then((post) => {
    console.log(post.title);
  })
  .catch((error) => {
    console.log(error);
  });
```

<br>

**[POST 요청]**

GET 요청과는 다르게, 두 번째 인자로 조건을 담은 객체를 전달해야 한다.

1\. POST 메서드를 사용한다고 선언하기  
2\. 새로운 블로그 게시물을 생성하는 JSON 데이터를 넘겨주기  
3\. 헤더의 Content-Type을 application/json으로 설정하기: JSON 데이터를 보내기 때문  
4\. JSON 데이터를 담은 문자열로 요청 본문 추가하기

```js
const newPost = {
  title: "jinsil",
  body: "Gonna be Best Part",
  userId: "1",
};

const option = {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify(newPost),
};

fetch("https://jsonplaceholder.typicode.com/posts/1", option)
  .then((data) => {
    if (!data.ok) {
      throw new Error(data.status);
    }
    return data.json();
  })
  .then((post) => {
    console.log(post.title);
  })
  .catch((error) => {
    console.log(error);
  });
```

<br>
<br>

### <mark class="yellow">4. Async Await</mark>

이런 Promise는 **async/await**을 이용해서 더욱 편하게 다룰 수 있게 발전했다.

Promise는 콜백과 비교하면 엄청난 발전이지만, 인터페이스가 여전히 다소 투박하다. 그리고 Promise를 사용해도 여전히 메서드에서 콜백을 다뤄야 한다.

async 키워드를 이용해서 선언한 함수는 비동기 데이터를 사용한다는 것을 의미한다. 비동기 함수의 내부에서 await 키워드를 사용하면 값이 반환될 때까지 함수의 실행을 중지시킬 수 있다.

```js
async function getUser() {
  const eastjun = await getTargetUser();
  return eastjun;
}
```

비동기 함수도 Promise로 변환된다. 즉 getUser()을 호출하는 경우 then() 메서드가 필요하다.

<br>

async 함수가 유용할 때는 여러 개의 Promise를 다룰 때이다.

```js
// async
async function getArtistPreference() {
  const theme = await getUSerPreferences();
  const album = await getMusic(theme);
  const artist = await getArtist(album);
  return artist;
}

// Promise
getArtistPreference()
  .then((artist) => console.log(artist))
  .catch((error) => console.error(error));
```

<br>
<br>

## <mark class="pink">📖비동기 퀴즈</mark>

### <mark class="yellow">Q1. setTimeout과 Promise</mark>

**[문제]**

왜 1->3->2->4 순으로 출력되는지 설명하시오.

```js
console.log("1");
setTimeout(() => console.log("2"));
new Promise((resolve) => resolve()).then(() => {
  console.log("3");
});
setTimeout(() => console.log("4"));
```

<br>

**[답]**

<br>
<br>

### <mark class="yellow">Q2. 렉시컬 환경</mark>

**[문제]**

왜 두 개의 for문의 결과가 다른지 설명하시오.

```js
for (var i = 0; i < 10; i++) {
  setTimeout(() => console.log(i)); // 10이 10번 출력
}
```

```js
for (var i = 0; i < 10; i++) {
  (function (i) {
    setTimeout(() => console.log(i)); // 0 1 2 3 4 5 6 7 8 9
  })(i);
}
```

<br>

**[답]**

<br>
<br>

### <mark class="yellow">Q3. async와 비동기</mark>

**[문제]**

foo 함수에서 getPopularMovies를 호출한 결괏값을 받아서 console에 출력하시오.

왜 이렇게 짜서 왜 이렇게 작동하는지 설명도 덧붙여야 합니다.

단, foo 함수는 async 키워드를 붙일 수 없다.

```js
async function getPopularMovies() {
  await new Promise((resolve) => setTimeout(resolve, 1000));
  return [
    "쿵푸팬더4",
    "댐즐",
    "스리 아시",
    "돼지와 뱀과 비둘기",
    "듄: 파트 2",
    "가여운 것들",
  ];
}

function foo() {
  //✨✨✨✨✨✨✨✨✨✨✨✨✨✨ 수정 가능 영역
  //✨✨✨✨✨✨✨✨✨✨✨✨✨✨
}
foo();
```

<br>

**[답]**

<br>
<br>

### <mark class="yellow">Q4. 에러 처리</mark>

**[문제]**

getPopularMovies 함수가 3초 이상 지연되면 "대기 시간이 오래 걸립니다"라는 오류 메시지를 콘솔로 출력하시오.

왜 이렇게 짜서 왜 이렇게 작동하는지 설명도 덧붙여야 합니다.

(단, 3초 이상 지연 시 getPopularMovies는 작업이 중단되거나 결괏값은 무시된다.)

```js
console.error("대기 시간이 오래 걸립니다");
```

아래의 영역에 코드를 채워 넣고 왜 해당 코드를 넣으면 해결되는지 설명하시오.

```js
const getPopularMovies = new Promise((resolve, reject) => {
  setTimeout(resolve, Math.random() * 4000 + 1000, "외부 API 접근한 결괏값");
});

// 아래에 답안을 작성하세요
```

<br>

**[답]**
