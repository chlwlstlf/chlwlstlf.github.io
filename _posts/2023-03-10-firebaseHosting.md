---
layout: single
title:  "React+구글 Firebase로 배포하기"
categories: React
toc: true
toc_sticky: true
---

# Firebase Hosting 서비스

## 프로젝트 만들기

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <div>1. firebase에 회원가입 & 로그인</div>
</div>

***

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <div>2. 프로젝트 추가 클릭</div>
</div>

  ![1](https://github.com/chlwlstlf/portfolio/assets/63334368/a5df2403-3100-4318-bdfd-f4efbf3106ef)

***

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>3. 프로젝트 이름 지정</p>
  <div>※아래에 연필 아이콘 뒤에 나오는 것이 링크 이름</div>
  <div>파이어베이스에서 생성한 적이 없는 프로젝트 이름이면 프로젝트 이름이 링크 이름이지만,</div>
  <div>이미 있는 이름이면 test-8b105 이런식으로 프로젝트 이름 뒤에 임의의 코드가 붙게 된다.</div>
</div>

  ![2](https://github.com/chlwlstlf/portfolio/assets/63334368/6bacd937-c0a4-4e6f-8613-e6a2cc2cb5c7)

***

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <div>4. 애널리틱스 사용은 본인 선택, `계속` 버튼 클릭</div>
</div>

  ![3](https://github.com/chlwlstlf/portfolio/assets/63334368/d171ac4d-c594-4fdc-ad10-c2c2a123be1d)

***

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <div>5. 구글 계정 선택 후 `프로젝트 만들기` 클릭</div>
</div> 

  ![4](https://github.com/chlwlstlf/portfolio/assets/63334368/72853f07-cbca-4ac2-a83d-97cc78b9e814)

***

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <div>6. 로딩 후 프로젝트가 생성됨</div>
</div>

  ![5](https://github.com/chlwlstlf/portfolio/assets/63334368/254661d1-3d41-4c46-99bf-7b92bb9b2fea)

  ![6](https://github.com/chlwlstlf/portfolio/assets/63334368/4078632a-c1ff-45cc-909c-c481d802491b)

***

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <div>7. 프로젝트가 생성되면 프로젝트의 첫 화면으로 옴</div>
</div>

  ![7](https://github.com/chlwlstlf/portfolio/assets/63334368/5d326a38-5859-41bf-9bff-622b09150ec9)

***

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <div>8. 좌측 메뉴 칸에서 `빌드-Hosting`을 누른 후 `시작하기` 버튼 클릭</div>
</div>

  ![8](https://github.com/chlwlstlf/portfolio/assets/63334368/a40307b8-1786-44b0-96df-4c88cd574418)

***

## 호스팅 설정하기

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <div>1. npm install -g firebase-tools 복붙하기</div>
</div>

  ![9](https://github.com/chlwlstlf/portfolio/assets/63334368/22bb3abf-b7e1-4c0a-a4e3-bd52264c1cc7)

  ![10](https://github.com/chlwlstlf/portfolio/assets/63334368/0424a0a6-0723-4053-8ce2-165bd1c9c1ad)

***

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>2. firebase login 복붙하기</p>
  <div>yes → 프로젝트 만든 계정 선택 → 성공 창 뜸</div>
</div>

  ![11](https://github.com/chlwlstlf/portfolio/assets/63334368/f27a60d4-9cc3-47b5-a6a6-baedcfdf8560)

  ![14](https://github.com/chlwlstlf/portfolio/assets/63334368/cb60642b-9fbc-4994-a38f-41a1e8e45125)

  ![12](https://github.com/chlwlstlf/portfolio/assets/63334368/5efe6e48-c18f-4784-bcb8-828eb25211e6)

  ![13](https://github.com/chlwlstlf/portfolio/assets/63334368/392ff76b-825e-4dec-8b31-81eaf5a32341)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>3. firebase init 복붙하기</p>
  <div>1) 진행할 준비 되었나요? yes</div>
  <div>2) 밑으로 내리는건 방향키 ↓, 선택은 space bar → hosting 두 개를 선택한다</div>
  <div>3) 위에서 프로젝트를 생성했으니 Use an exisiting project 선택</div>
  <div>4) 배포할 프로젝트 이름 선택</div>
  <div>5) publice 디렉토리에 할건가요? build</div>
  <div>깃허브는 선택이고 나머지는 그림과 같이 하면 된다.</div>
</div> 

  ![15](https://github.com/chlwlstlf/portfolio/assets/63334368/714aea30-4090-489f-92a3-246765d4a97f)

## 배포하기

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>1. npm run build</p>
  <div>수정한 내용을 배포할 파일에 빌드하기</div>
</div>

  ![17](https://github.com/chlwlstlf/portfolio/assets/63334368/c26230ba-24d7-4d27-862f-83a226139968)


<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>2. firebase deploy --only hosting</p>
  <div>firebase deploy로 하면 요금제로 전환하라는 문구가 뜸</div>
  <div>hosting은 무료 서비스이기 때문에 요금제가 필요 없으므로 hosting만 배포하면 됨</div>
</div>

![16](https://github.com/chlwlstlf/portfolio/assets/63334368/3bffcc6b-b821-41c0-9963-f3a7f33016a8)

![18](https://github.com/chlwlstlf/portfolio/assets/63334368/d6bc9ff5-9899-4531-bc48-17719bffc8b8)

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
  <p>3. Hosting URL에 있는 링크를 누르면 배포가 된 걸 볼 수 있음</p>
  <a href="https://choijinsilportfolio.web.app">https://choijinsilportfolio.web.app</div>
</div>

![19](https://github.com/chlwlstlf/portfolio/assets/63334368/d1fbac85-9d86-4396-b506-f4200caae278)