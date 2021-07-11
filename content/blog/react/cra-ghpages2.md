---
title: "React application SPA with gh-pages deploy(2)"
date: 2019-01-13 21:00:00
category: react
thumbnail: { thumbnailSrc }
draft: false
---

이전 [포스팅](https://juunone.github.io/cra-ghpages/)에서 CRA(create-react-app)을 통해
생성된 어플리케이션을 homepage에 호스팅 해봤습니다.
이번 포스팅은 해당 어플리케이션을 react-router-dom 을 통해 SPA(single application app)으로 구현하는 튜토리얼을 진행해보겠습니다.

> DEMO 페이지는
> <a href="https://juunone.github.io/react-spa-ghpages/#/" taget="_blank">react-spa-ghpages</a>에서 확인가능합니다.

- 리액트 라우터와 간단한 화면 구성을 위해 semantic-ui를 설치해줍니다

<pre><code><span style="color:orange">yarn</span> add react-router-dom semantic-ui-react</code></pre>

- 프로젝트 구조는 아래와 같습니다

```
|-- node_modules
|-- public
    |-- index.html
    |-- favicon.ico
|-- src
    |-- App.js
    |-- App.css
    |-- About.js
    |-- Home.js
    |-- NotFound.js
    |-- index.js
    |-- index.css
|-- package.json
|-- READEME.MD
|-- yarn.lock
```

- 리액트 라우터를 App.js에 연결해주고 보여줄 컴포넌트 및 경로 설정을합니다.
- BrowserRouter가 gh-pages 에서 정상 작동되지 않으므로 react-router-dom에서 HashRouter를 대체합니다.
  {HashRouter}를 'react-router-dom'에서 가져 오면 경로에 # 기호가 생깁니다.
- App.js 를 아래와 같이 변경합니다.

```jsx
import React, { Component } from "react";
import { Switch, HashRouter as Router, Route } from "react-router-dom";
import logo from "./logo.svg";
import "./App.css";

import About from "./About";
import Home from "./Home";
import NotFound from "./NotFound";

class App extends Component {
  render() {
    return (
      <Router>
        <div>
          <Switch>
            <Route exact path="/" component={Home} />
            <Route exact path="/about" component={About} />
            <Route component={NotFound} />
          </Switch>
        </div>
      </Router>
    );
  }
}

export default App;
```

- /src 폴더로 이동후 js 파일을 생성합니다.

```
touch Home.js About.js NotFound.js
```

- 앱 내에서 다른 라우트로 이동할경우, a태그를 이용하게되면 웹을 새로고침하게 됩니다.
  리액트 라우터의 Link 컴포넌트를 사용합니다. 이 컴포넌트는 새로고침없이 라우팅하게 됩니다.
- Home.js 에 코드를 추가합니다.

```jsx
import React, { Component } from "react";
import { Link } from "react-router-dom";

class Home extends Component {
  render() {
    return (
      <div>
        <p>Hello World!</p>
        <p>
          <Link to="/about">about 페이지로 이동</Link>
        </p>
      </div>
    );
  }
}

export default Home;
```

- About.js 에 코드를 추가합니다.

```jsx
import React, { Component } from "react";
import { Header } from "semantic-ui-react";

class About extends Component {
  render() {
    return (
      <div>
        <Header as="h2">About Page</Header>
        <p>Page is change without reload!</p>
      </div>
    );
  }
}

export default About;
```

- NotFound.js 에 코드를 추가합니다.

```jsx
import React, { Component } from "react";
import { Icon, Header } from "semantic-ui-react";

class NotFound extends Component {
  render() {
    return (
      <div>
        <Icon name="minus circle" color="red" size="small" />
        <strong>Page not found!</strong>
      </div>
    );
  }
}

export default NotFound;
```

> path 를 통해 해당 경로와 일치하는 컴포넌트를 렌더링해주고 그외는 모두 NotFound 가 렌더링 되게 됩니다.

- 사전배포 & 배포 차례대로 실행해보세요.

<pre><code><span style="color:orange">yarn</span> predeploy</code><code><span style="color:orange">yarn</span> deploy</code></pre>

CRA로 만든 앱이 해당 homepage 에 호스팅 된걸 확인할 수 있습니다. <a href="<<https://juunone.github.io/react-spa-ghpages>>" target="\_blank">https://juunone.github.io/react-spa-ghpages</a>

참고 : 현재 데모링크로 올려놓은 페이지의경우는 cra + webpack + react-router-dom + gh-pages 로 구성된
SPA 환경입니다.
