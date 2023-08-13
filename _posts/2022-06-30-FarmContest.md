---
layout: archive
title: "2022 농림축산식품 공공빅데이터 활용 창업경진대회"
categories: project
toc: true
toc_sticky: true
---

# 제품 및 서비스 개발 부분

[공모전 사이트](https://data.mafra.go.kr/contest/introduction/introductionNew/screen.do){: .btn .btn--primary}
[공모전 노션](https://polite-rambutan-6e0.notion.site/b498691ea2124e23ac4fb7246538cd5f){: .btn .btn--primary}
[공모전 깃허브](https://github.com/farmfarmfarmfarm){: .btn .btn--primary}

## <mark style='background-color: #ffdce0'> 소개 </mark>

‘치유농업 연구개발 및 육성에 관한 법률(이하 치유농업법)’이 국회를 통과할 만큼 치유농업에 대해 국가가 관심을 가지며 치유농업을 알리고 있지만, 치유농업의 개념조차도 인식하지 못하는 사람들이 많습니다. 이에 농업 활동으로 힐링하는 시간을 얻을 수 있도록 도와주는 서비스를 기획하였습니다.

Therapia는 라틴어로 '치료'를 의미합니다. 저희 서비스에서 이 치료는 두 가지의 의미를 담고 있습니다.  
첫 번째는 '치유농장'을 통한 심적인 치료를 할 수 있다는 의미이고,  
두 번째는 '작물 진단'을 통한 신체적인 치료를 할 수 있다는 의미입니다.

(깃허브 README에 기능 상세 있음)

---

## <mark style='background-color: #ffdce0'> 기술 </mark>

1. React
2. Typescript
3. Javascript
4. Recoil

---

## <mark style='background-color: #ffdce0'> 트러블 슈팅 </mark>

1. <mark style='background-color: #fff5b1'>전역 변수 필요 (useState)</mark>

- 문제 : function 안에서 `const num = 1` 이라고 선언하면 함수 밖에서는 그 변수를 사용하지 못 한다
- 해결 : `const [num, setNum] = useState(0);` 은 전역변수처럼 사용 가능

2. <mark style='background-color: #fff5b1'>비동기 해결 (useEffect)</mark>

- 문제 : api를 여러개 불러올수록 비동기의 문제점이 커진다  
  화면이 뜨자마자 데이터를 불러오고 싶으면?
- 해결 : useEffect 안에 불러오고 싶은 api 코드를 넣으면 된다

3. <mark style='background-color: #fff5b1'>api null ({ && })</mark>

- 문제 : api로 받아온 값이 잘 들어가 있는데 null값이라고 에러가 뜬다
- 해결 : `{변수 && }` 이런식으로 변수가 null이 아닐 때 출력한다고 조건을 걸어주면 잘 실행된다

4. <mark style='background-color: #fff5b1'>고유 주소 (useParams)</mark>

- 문제 : 각 작물을 누르면 그 작물의 레시피로 넘어가고 싶은데 작물id를 어떻게 넘겨야할 지 고민했다
- 해결 : `/:cropId`로 넘겨주고 `params = useParams()`로 받는다

5. <mark style='background-color: #fff5b1'>props가 안 될 때(Recoil)</mark>

- 문제 : 형제/자식->부모 컴포넌트에서는 props 전달이 안된다
- 해결 : Redux를 사용하려 했지만 코드짜는 데 어려움이 있어서 최신 라이브러리인 Recoil을 사용했다

6. <mark style='background-color: #fff5b1'>cors에러 (프록시 방식)</mark>

- 문제 : 개발할 때 무조건 한번씩 보는 cors에러가 어김없이 등장했다
- 해결 : setupProxy.js 파일을 만들어서 사람들의 코드를 그대로 넣었다
    <details>
      <summary>※cors 에러※</summary>
      <div markdown="1">       
        CORS 에러는 브라우저에서 서로 다른 도메인/포트의 서버로 요청이 갈때 브라우저에서 발생
      </div>
    </details>

## <mark style='background-color: #ffdce0'> 후기 </mark>

리액트를 2주 공부하고 바로 공모전을 준비했던 시기라 코드를 "잘" 짠다는 게 뭔지 몰랐다.
파이썬처럼 시간 복잡도가 눈으로 보이는 것도 아니고 리액트적인 코드가 뭔지 감도 오지 않았다.

구글링으로 사람들의 코드를 열심히 분석하고 복붙으로도 실행을 성공시키려고 노력했다.
사실 이게 도움이 컸던 것 같다. 코딩은 이론만 알고 바로 실습으로 넘어갈 수 없는 부분이니까
