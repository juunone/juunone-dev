---
title: "How to add Google reCAPTCHA"
date: 2020-05-08 12:00:00
category: web
thumbnail: { thumbnailSrc }
draft: false
---

## Google reCAPTCHA

로봇을 이용한 회원가입 및 로그인 방지를 위해 구글리캡차를 도입한 경험을 공유한다.

회원가입같은 보안이슈가 동반되는 페이지는 무조건적으로라도  
인증을 통해 가입을 가능하게 만드는것이 최소한의 조건이라고 개인적으로 생각한다.  
리캡차를 도입한다고해도 100% 로봇을 통한 가입을 제한한다고 볼수 없지만  
최소한의 비용으로 하나의 인증을 추가할 수 있다면 리캡차도 그중 하나의 방법이라고 생각한다.

**또한 리캡차는 대중적인 언어들은 모두 지원한다.**


### 참조
- [Google reCAPTCHA](https://www.google.com/recaptcha/intro/v3.html)
- [reCAPTCHA Help](https://support.google.com/recaptcha/?hl=en)
- npm modules
  - [react-google-recaptcha](https://www.npmjs.com/package/react-google-recaptcha)
  - [react-recaptcha-google](https://www.npmjs.com/package/react-recaptcha-google)
  - [react-recaptcha-v3](https://www.npmjs.com/package/react-recaptcha-v3)

## What is reCAPTCHA?

[What is reCAPTCHA](https://developers.google.com/recaptcha/?hl=ko)를 보면 동영상으로 친절하게 리캡차에 대한 설명을 해준다.

웹 클라이언트에서 100% 보안 이슈를 해결할 수 있다고 장담할 수 없다.  
하지만 로그인이나 회원가입 같은 auth 들은 최소한의 보안이라도 갖춰놔야지
좀더 안정성있고, 좋은 환경을 유저들에게 제공할 수 있다.
> e.g. 오래된 게시판에 광고글도 모두 글을 등록하는 시점에 로봇으로 등록이 가능하기 때문에 보안이 더 필요하다.

## Settings

가입하고 세팅에 들어가면
- keys
- domains

위 2가지를 설정해야 한다.
먼저 구글캡차를 이용하기 위해서는 발급받은 키를 사용해서 모듈을 호출해서 사용해야하며,
서버사이드 처리없이 클라이언트에서만 처리할경우 `사이트 키`만 사용하면 된다.


- 발급된 키를 확인할 수 있는 영역  
![setting](https://user-images.githubusercontent.com/58495926/81540489-b2b3b080-93ac-11ea-904c-73edb6365454.jpg)


- 등록된 도메인에 한해서 구글캡차를 사용할 수 있음  
참고로 `localhost`, `127.0.0.1` 을 등록하면 로컬 개발환경에서도 테스트 할 수 있다.
![setting2](https://user-images.githubusercontent.com/58495926/81541089-8fd5cc00-93ad-11ea-8f19-d24c79d84bee.jpg)

## How to use it?

내가 사용한 라이브러리로는 위에서 언급한 `react-google-recaptcha`를 사용했다.  
주단위 다운로드도 약 16만회에 달하고, 컴포넌트 단위로 부분적으로 적용할 수 있어서  
현재 로그인/회원가입에만 적용이 필요했던 현재 상황과 일치했다.  
물론 친절한 README는 덤.

- 사이트키는 env에 넣어두고 사용했다.
- `hl`은 [reCAPTCHA 언어코드](https://developers.google.com/recaptcha/docs/language)에서 사용중인 언어 property로 기존에 i18n에서 사용중인 랭기지코드를 가져왔다.
- `theme` 는 총 2가지를 제공해주며, `light` , `dark` 를 사용해 각 웹사이트 테마에 맞게 사용할 수 있다. 
- 콜백함수 종류
  - onChange={val => {}} 리캡차 체크박스가 체크된 시점 콜백 함수
  - onExpired={exp => {}} 체크박스 체크후에 일정시간이 지난후 리캡차가 만료된 시점 콜백 함수
  - onErrored={err => {}} 리캡차에 오류 (일반적으로 네트워크 연결)가 발생하여 연결이 복원 될 때까지 계속할 수 없을 때 실행되는 콜백 함수 이름
  - 리캡차에 대한 validation을 클라이언트에서 가지고 있을 경우 보통 `onChange`, `onExpired`에서 처리해준다.

> 다크 테마 리캡차 이미지 참고  
![theme img](https://user-images.githubusercontent.com/58495926/81629834-15ee2300-943f-11ea-94d9-551fff662fe5.png)


```js
import ReCAPTCHA from "react-google-recaptcha";

<ReCAPTCHA
  sitekey={process.env.REACT_APP_RECAPTCHA_SITE_KEY}
  onChange={val => {}}
  onExpired={exp => {}}
  onErrored={err => {}}
  hl={i18n.language.split("-")[0]}
  theme="dark"
/>
```

react에서는 `onChange` 함수 콜백을 통해 회원가입 버튼 활성화를 시켜주는 보통의 방법으로, 클라이언트에서 제어가 가능하다.  
반대로 `onExpired`에서 회원가입 만료시 재 인증하도록 요구하는 절차를 포함시킬 수 있다.