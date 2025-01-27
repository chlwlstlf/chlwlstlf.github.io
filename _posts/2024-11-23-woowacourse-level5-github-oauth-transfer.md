---
layout: single
title: "[우테코] Level5 Github OAuth 소유권 이전"
categories: woowacourse
toc: true
toc_sticky: true
---

# 코레아 계정으로 Github OAuth transfer 하기

## <mark class="pink">📌들어가기</mark>

처음 Github OAuth로 여러 실험을 해보았는데 그땐 CoReA 깃허브 계정이 없었다.  
그 이유로 우선 내 계정으로 OAuth를 만든 다음에 성공하면 CoReA 깃허브 계정을 만들어 그것을 연결하려고 하였다.

하지만 실험 도중 모든 팀원들이 내 OAuth로 로그인을 성공하여 캐싱되었다. 아래 화면인 어느 OAuth로 로그인이 되는지 보이는 `github OAuth 링크`를 거치지 않고 바로 로그인이 되었고, CoReA 계정으로 연결하는 것을 모두가 잊어버렸다.

![들어가기1](https://github.com/user-attachments/assets/8c5dee22-cc68-4eb8-a308-abdf37e4bb2d)

<div class="blue-box">
  <div>'Settings > Applications > Authorized OAuth Apps'로 들어가면 내가 어느 사이트에 Github 로그인을 했는 지 볼 수 있다.</div>
  <div>여기에 있는 OAuth면 해당 서비스에 캐싱되어 있는 것이다.</div>
</div>

<br>
<br>

## <mark class="pink">📌OAuth 앱의 소유권 이전</mark>

[깃허브 OAuth 앱의 소유권 이전](https://docs.github.com/ko/apps/oauth-apps/maintaining-oauth-apps/transferring-ownership-of-an-oauth-app)

![이전1](https://github.com/user-attachments/assets/7703765a-b002-4011-ab8c-68de346acbef)

위 링크를 누르면 소유권 이전을 어떻게 하는지 나온다. 하지만 내가 궁금한 건 Client ID, Client secrets, users 수가 모두 동일하게 이전이 되는지 였다. 이에 대해 작성한 블로그가 없어 떨리는 마음으로 dev 서버부터 이전을 해보았다.

결론부터 말하자면 Client ID, Client secrets, users 수 모두 그대로 이전이 되고, FE와 BE는 이에 대해 추가적인 작업을 할 필요가 없다.

<br>
<br>

## <mark class="pink">📌이전 실습</mark>

1\. 화면에 보이는 `prod-CoReA` OAuth를 `2024-Code-Review-Area` 계정으로 옮길 예정이다.

![실습1](https://github.com/user-attachments/assets/c6ff82d1-fcda-4048-bd0b-e72161f7ae39)

<br>

2\. `prod-CoReA`를 들어가서 `Transfer ownership`을 누르면 모달이 뜬다.

![실습2](https://github.com/user-attachments/assets/a765c960-d6aa-4ad4-abfe-b542f57d4f47)

<br>

3\. 이전할 OAuth(`prod-CoReA`)와 어느 계정으로 이전할 지(`2024-Code-Review-Area`)를 적어주면 된다.

![실습3](https://github.com/user-attachments/assets/e5e5cc45-cdc1-4a1b-9e55-c3a61d8f0c6d)

<br>

4\. 상단에 `2024-Code-Review-Area`에 이전 요청을 보냈다고 뜨고, pending 상태라고 한다.

![실습4](https://github.com/user-attachments/assets/2ab4d96d-03fc-4192-9122-c2d7f5a4cacb)

<br>

5\. 요청을 수락하러 `2024-Code-Review-Area` 계정으로 로그인을 했다. 이전 요청 pending 상태라는 문구를 볼 수 있다.

![실습5](https://github.com/user-attachments/assets/d6f935bf-1ab1-40ce-896a-97216514cd30)

<br>

6\. `prod-CoReA`를 클릭하면 Client ID와 users 수가 그대로 이전된 것을 볼 수 있다. `Complete transfer`를 클릭하여 요청을 수락한다.

![실습6](https://github.com/user-attachments/assets/30c3b417-55b5-48ba-af32-39c467f500b4)

<br>

7\. Client secrets는 마스킹 처리가 안 되어있는 뒷 부분만 봤을 때 같은 값이기 때문이 동일하다고 판단했다.

![실습7](https://github.com/user-attachments/assets/fb65ee63-ed7b-4bee-98aa-03baadae25b9)

<br>
<br>

## <mark class="pink">📌결과 화면</mark>

[CoReA](https://code-review-area.com/)에서 `2024-Code-Review-Area`의 `prod-CoReA`로 로그인이 되는 것을 볼 수 있다.  
317 users 유지 성공❗

![결과1](https://github.com/user-attachments/assets/0f482d00-3afb-4836-a11d-ba6f4231a8fa)
