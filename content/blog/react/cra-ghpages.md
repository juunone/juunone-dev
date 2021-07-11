---
title: "React application SPA with gh-pages deploy(1)"
date: 2019-01-12 21:00:00
category: react
thumbnail: { thumbnailSrc }
draft: false
---

<h3>리액트를 경험해본 분들을 대상으로 SPA 환경을 구축한후 gh-pages를 사용해 깃헙 레포지터리에 환경을 만드는 과정을 소개합니다. <br />
구글에 해당 부분 관련해서 영문 문서 간략하게 번역 및 재해석이 들어가있습니다.<br />
</h3>
> DEMO 페이지는
<a href="https://juunone.github.io/react-spa-ghpages/#/" taget="_blank">react-spa-ghpages</a>에서 확인가능합니다.

- create-react-app 을 통해서 어플리케이션을 만듭니다.

<pre><code><span style="color:orange">create-react-app</span> cra-gh-pages</code></pre>

- 깃헙 저장소 생성
- gh-pages 설치합니다. [gh-pages 확인](https://www.npmjs.com/package/gh-pages)
  - gh-pages는 npm 패키지로 Github에 gh-pages라는 브랜치를 생성해 파일을 게시할수있게 도와줍니다.

<pre><code>cd cra-gh-pages</code><code><span style="color:orange">yarn</span> add gh-pages</code></pre>

- CRA로 생성된 폴더에 가서 package.json 에 접근합니다. 이동할 레포지터리 이름을 hompage 코드에 추가해줍니다.
- scripts 부분에 predeploy 및 deploy 코드를 추가합니다.

```javascript
{
    "name": "cra-ghpages",
    "version": "0.1.0",
    "private": true,
    "homepage": "https://[username].github.io/[repository]",
}
```

```
{
   ...
   "predeploy": "yarn run build",
   "deploy": "gh-pages -d build",
   ...
}
```

> predeploy 는 배포하기전 번들된 파일을 다시 빌드하기위해 수행하는 명렁입니다.<br />
> deploy 는 ghpages 명령어를 통해 build 폴더에 위치한 파일을 package.json > homepage 주소로 publish 하게 됩니다.

- 배포전 리모트 저장소에 origin 을 추가해주세요.

<pre><code><span style="color:orange">git</span> remote add origin https://github.com/[username]/[repository].git</code></pre>

- 사전배포 & 배포 차례대로 실행해보세요.

<pre><code><span style="color:orange">yarn</span> predeploy</code><code><span style="color:orange">yarn</span> deploy</code></pre>

CRA로 만든 앱이 해당 homepage 에 호스팅 된걸 확인할 수 있습니다. <a href="<<https://juunone.github.io/react-spa-ghpages>>" target="\_blank">https://juunone.github.io/react-spa-ghpages</a>

다음 포스팅에서 react-rotuer-dom 연결을 통해 SPA 환경을 구축해보겠습니다.
