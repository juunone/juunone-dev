---
title: "React editor [Dynamic contents editor]"
date: 2018-02-08 14:00:00
category: development
thumbnail: { thumbnailSrc }
draft: false
---

개발이 완료된 제품에 대한 이해를 돕기위해 제작된 개발 정의서입니다.

[DCE 홍보영상 보러가기](https://drive.google.com/open?id=10FZJ6TeNIc8GWNhcZb01oWEZH3Hn8mr5)

## **배경**

---

처음 문제는 **_Targetbook_** 으로부터 시작되었습니다.<br />
문제의 배경은 광고를 집행하고 운영하는 AE 분들에 의해서 발의되었습니다.<br />
이번 사례의 경우, 아래와 같이 배경이 정리 될 수 있다.

- 페이스북과 동일한 [캔버스](https://www.facebook.com/business/learn/facebook-create-ad-canvas-ads) 기능을 사용할 수 있을것
- 랜딩페이지 유입을 높이는 것
- ~~재밌겠다~~

## **문제정의**

---

효과적으로 빠르게 광고 랜딩페이지 제작 목적을 띄고있는 페이스북의 [캔버스](https://www.facebook.com/business/learn/facebook-create-ad-canvas-ads)<br />
기능이 타겟북 어드민내에 존재하지 않는다.

- 페이스북 캔버스 Dependency
- 기존 타겟북 랜딩페이지와의 차별성을 가지고 insight 도출

## **예상 및 해결**

---

목적 설정 과정에 중점을 두고 시작했다.<br />
어쨌든 랜딩 페이지 목적을 띈 CMS를 개발해야 되는 입장으로서
페이스북에 종속되어있는 캔버스 기능을 한땀한땀 개발하기에는 시간 및 유지보수에
큰 문제점을 느끼고 **Targetbook+iMs** 모두에서 사용할 수 있는 이른바<br />
~~DbodyCMS~~(초기명칭) = **DCE** 제품이 개발된다.

## **Flowchart**

![다이어그램](https://user-images.githubusercontent.com/35126809/64483862-016ce180-d245-11e9-8540-7936f7d42d0c.png)

## **Use case diagram**

![유즈케이스](https://user-images.githubusercontent.com/35126809/64483863-016ce180-d245-11e9-9c2a-5c77a855d273.png)

## **기술 스펙**

| name                 | version | dependencies    | role       |
| -------------------- | ------- | --------------- | ---------- |
| react                | ^16.2.0 | dependencies    | framework  |
| react-router         | ^4.2.0  | dependencies    | routing    |
| redux                | ^3      | dependencies    | state      |
| redux-thunk          | ^2.2.0  | dependencies    | middleware |
| redux-devtools       | ^3.4.1  | devDependencies |            |
| jquery               | ^3.2.1  | dependencies    | plugin     |
| babel-core           | ^6.26.0 | devDependencies | transpiler |
| babel-loader         | ^7.1.2  | devDependencies |            |
| babel-polyfill       | ^6.26.0 | devDependencies |            |
| babel-decorators     | ^1.3.4  | dependencies    | babelrc    |
| babel-preset-env     | ^1.6.1  | devDependencies | babelrc    |
| babel-preset-es2015  | ^6.24.1 | devDependencies | babelrc    |
| babel-preset-stage-0 | ^6.24.1 | devDependencies | babelrc    |
| css-loader           | ^0.28.7 | devDependencies |            |
| sass-loader          | ^6.0.6  | devDependencies |            |
| webpack              | ^3.10.0 | devDependencies | bundle     |
| webpack-dev-server   | ^2.9.7  | devDependencies |            |
| webpack-merge        | ^4.1.1  | devDependencies |            |

---

## **기술에 대한 의견**

> ## React
>
> - 목적
>   - 싱글페이지 어플리케이션 개발을 위한 자바스크립트 라이브러리 도입
> - 효과(주관적)
>   - React는 플레인 자바스크립트에 더 가깝다. 새로운 문법 내지는 컨벤션을 정의하는 대신 자바스크립트를 활용하는데 무리가 없다면 그리 한다
>   - React는 사용자 및 사용처에 대해 더 적은 가정을 하고, 컴포넌트 기반의 선언적 UI 렌더링이라는 가장 핵심적인 기능과 관련된 부분만 코어에 포함한다
>   - 매우 간편한 UI 수정 및 재사용(component)
>   - 페이스북이 밀어준다
> - 장단점
>   - [참조1](https://medium.com/@RianCommunity/react%EC%9D%98-%ED%83%84%EC%83%9D%EB%B0%B0%EA%B2%BD%EA%B3%BC-%ED%8A%B9%EC%A7%95-4190d47a28f)
>   - [참조2](https://joshua1988.github.io/web_dev/vue-or-react/)

> ## Redux
>
> - 목적
>   - 여러개의 스토어를 단 하나로 관리하기 위한 목적
> - 효과(주관적)
>   - 더 쉬운 글로벌 상태 관리를 위하여 리덕스를 사용하기도 하고, 조금 더 체계적이고 편리한 상태 관리를 하기 위하여 사용
>   - 시간 여행이 가능한 상태관리
>   - 많은 사용자들의 실행 액션을 받아 로컬의 작업과 병행 가능.
> - 장단점
>   - [참조1](http://ibrahimovic.tistory.com/31)
>   - [참조2](https://velopert.com/3533)

> ## Webpack
>
> - 목적
>   - 수십 혹은 수백개의 html,css,js 파일들의 통합 관리 목적
> - 효과(주관적)
>   - NPM으로 JavaScript 리소스 일원화
>   - Loader를 활용한 Node.js 도구의 활용
>   - 로컬/테스트/리얼 환경별 빌드 관리
>   - Webpack-dev-server 활용한 개발 편의성 증대
> - 장단점
>   - [참조1](https://medium.com/@ljs0705/spa-single-page-app-%EC%97%90%EC%84%9C-webpack%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0-ce7d3f82fe9)
>   - [참조2](https://dev.zzoman.com/2017/09/04/why-do-you-need-to-learn-about-webpack/)

> ## Babel
>
> - Babel은 javascript transpiler 이다.\
>   현재 자바스크립트 생태계에서는 선택이 아닌 필수로 되어가고있지만\
>   개인적으로 transpiler 에 의존성이 낮은 javascript 되었으면 한다.
> - [참조](https://moon9342.github.io/javascript-babel)

## **마치며**

제품을 개발하며 제일 주안점을 두었던 부분은 **_속도, 편의성, 확장성_** 이였다.

- 웹사이트 랜딩페이지의 속도는 무조건적으로 빨라야한다.
- HTML,CSS 를 모르는 사람도 사용할 수 있어야한다.
- 자사의 여러 서비스들에 적용이 가능해야한다.
