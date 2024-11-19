---
layout: single
title: "[우테코]Level3 Github 로그인"
categories: woowacourse
toc: true
toc_sticky: true
---

# Github OAuth로 로그인 구현

> Fe에게 이 글을 바칩니다^\_\_^

## <mark class="pink">📌OAuth 2.0 용어</mark>

**<mark class="yellow">Resource Owner</mark>**

- OAuth 2.0을 사용하여 보호되는 리소스에 대한 엑세스 권한을 부여하는 사용자
- 만약 Github 계정으로 로그인해서 현재 서비스를 사용한다면 서비스 관점에서 사용자는 Resource Owner라고 할 수 있습니다

<br>

**<mark class="yellow">Client</mark>**

- Resource Server의 자원을 이용하고자 하는 서비스
- 개발자가 OAuth를 사용해 개발하려는 서비스

<br>

**<mark class="yellow">Resource Server</mark>**

- 구글, 페이스북, 깃허브 등 리소스를 가지고 있는 서버

<br>

**<mark class="yellow">Authorization Server</mark>**

- Resource Owner를 인증하고, client에게 액세스 토큰을 발급해주는 서버
- 백엔드가 배포한 서버가 이에 해당합니다
- Authorization Server에 사용자 정보를 저장할 필요가 없다면 Client에서 바로 Resource Server로 연결하여 이를 사용하지 않을 수 있습니다

<br>

**<mark class="yellow">Access Token</mark>**

- Client가 Resource server에 접근하기 위한 권한을 부여받는 토큰
- 이 토큰을 header에 담아서 api 요청을 하면 Authorization server에서 사용자를 식별할 수 있습니다
- Authorization server로부터 발급되고, 일반적으로 짧은 유효 기간을 가지고 있습니다

<br>

**<mark class="yellow">Refresh Token</mark>**

- Access Token의 유효 기간이 만료된 후 새로운 Access token을 받기 위한 토큰
- Access Token에 비해 비교적 긴 유효 기간을 가지고 있습니다(일주일 ~ 1년)

<br>
<br>

## <mark class="pink">📌OAuth 로그인 과정</mark>

<div class="blue-box">
  <p>
    우리 서비스에서는 Authorization Server에 사용자 정보를 저장해야 하기 때문에 Authorization Server가 Resource Server로부터 발급받은 Access Token으로 사용자 정보를 받은 후 Authorization Server가 자체 생성한 Access Token과 Refresh Token, 그리고 응답 받음 사용자 정보를 Client에게 넘겨줍니다.
  </p>
</div>

![그림 1002](https://github.com/user-attachments/assets/d2bbe9f1-37cb-47ce-a320-a73cfc13cf97)

1\. Resource Owner는 로그인을 한 후 Resource Server로부터 code를 받습니다.

2\. Client는 이 code로 Authorization Server에 `로그인 post` 요청을 합니다.

3\. Authorization Server는 client ID, client secrets, code를 이용하여 Resource Server에 access Token을 요청합니다.

4\. Authorization Server는 발급 받은 access Token을 header에 담아서 Resource Server에 user Info를 요청합니다.

5\. Authorization Server는 자체 생성한 access Token, refresh Token과 Resource Server로 부터 받은 user Info를 Client에 넘겨줍니다.

6\. Client는 이를 기기에 저장한 후 access Token을 header에 담아서 api를 요청하는 데에 사용합니다.

<br>
<br>

## <mark class="pink">📌1. 깃허브 로그인 등록</mark>

[GitHub 앱 등록 공식 문서](https://docs.github.com/en/apps/creating-github-apps/registering-a-github-app/registering-a-github-app)

**<mark class="yellow">1. OAuth Apps로 들어가기</mark>**

Settings > Developer settings > OAuth Apps

<br>

**<mark class="yellow">2. New OAuth App</mark>**

**Application name**

- 서비스 이름 (중요x, 본인이 식별할 수 있는 이름이면 됩니다.)

**Homepage URL**

- 서비스 URL, 개발 중이므로 `http://localhost:3000`를 넣었습니다.

**Authorization callback URL**

- 미리 callback 페이지를 만들어두면 좋습니다.
- 개발 중이므로 `http://localhost:3000/callback`을 넣었습니다.
- 배포 시에는 배포한 url을 넣으면 됩니다.

![3](https://github.com/user-attachments/assets/174c5cc5-6a9a-4232-9612-84c75497e1aa)

<br>

**<mark class="yellow">3. Client ID, Client secrets</mark>**

> OAuth App을 생성하면 Client ID, Client secrets를 발급 받습니다.

**Client ID**

- Client에서 OAuth 화면으로 리다이렉트할 때 사용합니다.
- Authorization Server에서 Resource Server로 api를 요청할 때 사용합니다.

**Client secrets**

- 절대 유출되면 안 되는 정보이므로 처음 발급받을 때 이후로는 깃허브에서도 확인을 할 수 없습니다.
- Authorization Server에서 Resource Server로 api를 요청할 때 사용합니다.
- Authorization Server는 서브 모듈에 Client secrets를 저장합니다.

![4](https://github.com/user-attachments/assets/9beee5dc-8cd8-4981-b431-82f4b140239d)

<br>
<br>

## <mark class="pink">📌2. 로그인 클릭</mark>

- 로그인을 클릭합니다.
- 현재 localStorage는 비어있습니다.

![6](https://github.com/user-attachments/assets/34578ed3-569a-46aa-813b-e1c02d4007b8)

<br>
<br>

## <mark class="pink">📌3. Github로 리다이렉트</mark>

- Resource Owner(사용자)에게 인증을 요청합니다.
- 발급 받은 client_id와 백엔드와 정한 scope로 Github OAuth URL를 생성합니다.

![7](https://github.com/user-attachments/assets/09940612-5417-43e0-a7cb-34aac53b14fc)

<br>

**<mark class="yellow">로그인 코드</mark>**

```tsx
const Header = () => {
  const githubAuthUrl =
    "https://github.com/login/oauth/authorize?client_id=Ov23liFoUtO89lG70w9I&scope=user:email";

  const handleLogin = () => {
    window.open(githubAuthUrl, "_self");
  };

  return (
    <HeaderContainer>
      <HeaderItem onClick={handleLogin}>로그인</HeaderItem>
    </HeaderContainer>
  );
};

export default Header;
```

<br>
<br>

## <mark class="pink">📌4. Callback 페이지 화면</mark>

1\. Github Authorization callback URL에 지정한 callback 페이지로 넘어갑니다.

2\. callback url 뒤에 query 파라미터로 code가 넘어옵니다.

3\. 이 code를 추출하여 Authorization Server에 로그인 post 요청을 합니다.

![8](https://github.com/user-attachments/assets/8ec505c7-00ca-4e7b-a804-a708194988cb)

4\. 로그인 post 요청을 받은 Authorization Server는 client ID, client secrets, code를 이용하여 Resource Server에 [access Token을 요청](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/authorizing-oauth-apps)합니다.

![1000](https://github.com/user-attachments/assets/b85d482d-64b3-4c64-9845-38eab699e4dc)

5\. Authorization Server는 발급 받은 access Token을 header에 담아서 Resource Server에 [user Info를 요청](https://docs.github.com/ko/rest/users/users?apiVersion=2022-11-28)합니다.

![1001](https://github.com/user-attachments/assets/705b911f-a2b4-4bf7-869e-2665a9aec7ae)

6\. Authorization Server는 자체 생성한 access Token, refresh Token 그리고 Resource Server로 부터 받은 user Info를 Client에 넘겨줍니다.

7\. Client는 이를 기기에 저장한 후 access Token을 header에 담아서 api를 요청하는 데에 사용합니다.

![9](https://github.com/user-attachments/assets/7c294a7b-c2e4-4fd6-8451-26a1b1df349a)

<br>

**<mark class="yellow">Callback 페이지 코드</mark>**

```tsx
const CallbackPage = () => {
  const navigate = useNavigate();
  const { postLoginMutation } = useMutateLogin();

  useEffect(() => {
    const params = new URLSearchParams(window.location.search);
    const code = params.get("code"); // code 추출

    if (code) {
      postLoginMutation.mutate(code); // 로그인 post 요청
    }
  }, []);

  return <div>로그인 중...</div>;
};
```

<br>

**<mark class="yellow">로그인 요청 mutation 코드</mark>**

```ts
const postLoginMutation = useMutation({
  mutationFn: (code: string) => postLogin(code),
  onSuccess: ({
    accessToken,
    refreshToken,
    userInfo,
  }: {
    accessToken: string;
    refreshToken: string;
    userInfo: UserInfo;
  }) => {
    localStorage.setItem("accessToken", accessToken);
    localStorage.setItem("refreshToken", refreshToken);
    localStorage.setItem("userInfo", JSON.stringify(userInfo));
    navigate("/");
  },
  onError: (error) => {
    localStorage.clear();
    alert(error);
  },
});
```

<br>
<br>

## <mark class="pink">📌OAuth 로그아웃 과정</mark>

![101](https://github.com/user-attachments/assets/f6c5a585-9009-4a8c-b69e-0f31b743d1a8)

**refresh Token이 만료됐을 때**

1\. Client는 localStorage를 clear합니다.

2\. Authorization Server는 자동으로 DB에 있는 refresh Token을 삭제합니다.

<br>

**로그아웃을 눌렀을 때**

1\. Client는 localStorage를 clear한 후 `로그아웃 post`를 요청합니다.

2\. Authorization Server는 로그아웃 post를 요청 받으면 DB에 있는 refresh Token을 삭제합니다.

<br>
<br>

## <mark class="pink">📌1. 로그아웃 클릭</mark>

1\. Client는 localStorage를 clear한 후 `로그아웃 post`를 요청합니다.

2\. Authorization Server는 로그아웃 post를 요청 받으면 DB에 있는 refresh Token을 삭제합니다.

![10](https://github.com/user-attachments/assets/d4a4ed60-714a-4dd2-b80f-f66dbeb74aa1)

<br>

**<mark class="yellow">로그아웃 코드</mark>**

```tsx
const LogoutPage = () => {
  const { postLogoutMutation } = useMutateAuth();

  const handleLogoutClick = () => {
    postLogoutMutation.mutate(); // 로그아웃 post 요청
  };

  return (
    <LogoutContainer>
      <Item onClick={handleLogoutClick}>로그아웃</Item>
    </LogoutContainer>
  );
};

export default LogoutPage;
```

<br>

**<mark class="yellow">로그아웃 요청 mutation 코드</mark>**

```ts
const postLogoutMutation = useMutation({
  mutationFn: () => postLogout(),
  onSuccess: () => {
    localStorage.clear();
    navigate("/");
  },
  onError: (error) => alert(error),
});
```
