---
title: "dotenv 환경변수 관리하기"
date: 2019-11-24 17:00:00
category: react
thumbnail: { thumbnailSrc }
draft: false
---

*References*

[github dotenv](https://github.com/motdotla/dotenv)  
[crate-react-app dotenv](https://create-react-app.dev/docs/adding-custom-environment-variables/)

creat-react-app 으로 만든 프로젝트에서
환경변수를 개발 및 테스트 운영과의 완벽한 분리를 위해, 사용한 경험을
공유하기 위해 글을 작성한다.

## dotenv?

CRA 를 `eject` 해보면 dotenv 모듈이 설치된걸 확인할 수 있다.  
dotenv는 env를 손쉽게 사용할수 있도록 도와주는 helper 역할을 하는 모듈이다.
github에 적혀있는

Dotenv is a zero-dependency module that loads environment   variables from a .env file into process.env. Storing   configuration in the environment separate from code is based on The Twelve-Factor App methodology.

직역하자면 의존성 없이 .env 파일에 선언된 변수들을 `process.env` 로 불러와
사용할 수 있게 해주는 [The Twelve-Factor](https://12factor.net/config) 방법론에 기반한 모듈이라고 설명되어있다.

## how to use?

아래와 같이 .env 파일에서 선언된 변수들을 `process.env${variables}`
불러와서 사용할 수 있다. API endpoint등을 환경변수에 선언해놓고 사용하는
경우가 많다.
또한 index.html 에서는 변수 앞뒤에 `%` 키워드를 붙여 사용 가능하다.

```js
fetch(
`https://apiserver.${process.env.REACT_APP_BACKEND_API_URL}.demo.com/${url}`,
{
  method: 'GET',
  body: data
}
  )
  .then(response => response.json()) 
  .catch(error => {
    console.error("fetch Error:", error);
  })
  .then(response => {
    console.log("fetch Success:", JSON.stringify(response));
    return response;
});
```

```html
<title>%REACT_APP_WEBSITE_NAME%</title>
```

## how to configuration?

[env-cmd](https://www.npmjs.com/package/env-cmd) 같이 커맨드에 불러올 .env 환경설정파일을 설정할 수 있는 모듈도 존재한다.
> e.g: `env-cmd -f bin/.env.demo yarn build`

개인적인 견해로는 사용했던 방식중 제일 편하고 관리가 쉬었던 방법은

- .env
- .env.development , .env.production , .env.test
- .env.development.local , .env.production.local , .env.test.local

환경별 파일들을 생성해놓고 빌드커맨드에 따라서 불러오는 환경변수 설정파일을
다르게 설정한것이 유지보수하기 수월했다.

### 우선순위

아래와 같이 각 커맨드에 따라서 우선순위를 갖는 설정파일이 변경된다.
각각의 파일들만 설정해놓으면 커맨드마다 따로 설정파일을 설정하거나
실행할때마다 각 커맨드를 기억해서 터미널에 작성할 공수가 줄어든다.

`yarn start`

1. .env.development.local
2. .env.develpment
3. .env.local
4. .env

`yarn build`

1. .env.production.local
2. .env.production
3. .env.local
4. .env

`yarn test`

1. .env.test.local
2. .env.test
3. .env
