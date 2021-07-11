---
title: "styled components basics"
date: 2019-05-10 22:00:00
category: react
thumbnail: { thumbnailSrc }
draft: false
---

## what is styled-components?

컴포넌트 스타일링 방식 중 하나인, JS 코드 내부에서 스타일을 정의하는 방식입니다.
injectThemes 를 이용해 전체 테마를 관리 및 수정할 수 있고,
스타일의 재사용을 통해 리소스 낭비를 줄일 수 있다.
컴포넌트별 스타일의 재정의.  
**말그대로 JS로 다해버려.**

나는 개인적으로 sass처럼 단일 파일 관리를 선호하지만 알아두면 좋은 라이브러리다.
아래 작성돈 코드는 실제로 [GatsbyJS](https://www.gatsbyjs.org/) 로 만들어진 또 다른 [개발 블로그](https://juunone.netlify.com/)에 사용된 코드이다.

## styled.js

```js
import styled from 'styled-components';
import { rhythm } from "../utils/typography";

export const Header1 = styled.h1`
  /* Header1 상수에 h1 태그엘리먼트를 할당하고 폰트사이즈와 라인하이트의 css property를 선언한다 */
  font-size: 2rem;
  line-height: 1.4;
`;

export const Ul = styled.ul`
  margin-top:1em;
  list-style-position: inside;
`;

export const Paragraph = styled.p`
  margin-bottom:0;
`;

export const SocialLink = styled.span`
  margin-right:0.5em;
  color:#333;
  box-shadow:none;
  font-size:1.5rem;
`;

export const socialButton = styled.button`
  font-size:1.2em;
  background: #333;

  /* & 키워드로 해당 엘리먼트의 hover focus 등 선택자를 줄 수 있다.*/
  &:hover {
    background: #999;
  }
`;

export const Wrapper = styled.div`
  margin-left: auto;
  margin-right: auto;

  /* rhytm은 typography.js 에서 불러온 또다른 함수인데 스타일 컴포넌트는  외부 함수와 같이 사용할 수 있다.*/
  max-width: ${rhythm(24)};
  padding: ${rhythm(1.5)} ${rhythm(3 / 4)};

  /* props 로 전달받은 값을 css property에 선언할 수 있다. */
  font-family: ${props => props.theme.fontFamily};
`;
```

### layout.js

- 레이아웃을 담당하는 컴포넌트에 스타일컴포넌트가 어떤방식으로 적용되었는지
코드를 설명한다.

```js
import { ThemeProvider, createGlobalStyle } from 'styled-components';
import { Paragraph, SocialLink, Wrapper } from './styled'

// Define what props.theme will look like
// ThemeProvider를 이용해 앱의 전체 테마를 관리할 수 있다.
// 컬러등을 내려주며, 흔히 보이는 다크테마, 라이트테마등의 관리도 할 수 있게 활용 가능하다.
const theme = {
  fontFamily: "'Nanum Gothic', sans-serif;"
};

// 돔렌더링중 레이아웃후 페인트 과정에 입혀질 스타일에 대한 글로벌 스타일을 정의할 수 있다. 위 테마에서 정의된 폰트는 props로 받고 해당 css property에 정의할 수 있다. 그외 reset, normalize css 같은 초기화 부분도 담당한다.
const GlobalStyle = createGlobalStyle`
  body {
    font-family: ${props => props.theme.fontFamily};
    h3 { 
      font-family: ${props => props.theme.fontFamily};
    }
  }

  ul{
    margin-top:1em;
    list-style-position:initial;
    padding-left:1em;
  }
`
```

### Render

- `ThemeProvider`는 반드시 루트 컴포넌트여야 한다.
- `styled.js` 에서 정의된 `Wrapper,Paragraph,SocialLink` 컴포넌트들을 아래와 같이 렌더링 함수에 넣어 적용시켰다. 해당 컴포넌트들은
`styled.js` 에서 선언된 스타일들을 상속받는다.
- `createGlobalStyle` 는 루트컴포넌트 하위에 속해있으며, 사전에 선언된 스타일을 paint 시점에서 선언된다.

```js
render(){
  return (
    <ThemeProvider theme={theme}>
      <Wrapper>
        <GlobalStyle />
        <header>{header}</header>
        <main>{children}</main>
        <footer>
          <Paragraph>
            <SocialLink as="a" href={`https://github.com/${social.github}`} title="Github" target="_blank" rel="noopener noreferrer">
              <FontAwesomeIcon icon={faGithub} />
            </SocialLink>
            <SocialLink as="a" href={`https://www.linkedin.com/in/${social.linkedin}`} title="Linkedin" tSocialLinkrget="_blank" rel="noopener noreferrer">
              <FontAwesomeIcon icon={faLinkedin} />
            </SocialLink>
            <SocialLink as="a" href={`https://twitter.com/${social.twitter}`} title="Twitter" target="_blank" rel="noopener noreferrer">
              <FontAwesomeIcon icon={faTwitterSquare} />
            </SocialLink>
          </Paragraph>
          <Paragraph>
            <Link to='/about'>
              About
            </Link>
          </Paragraph>
          <Paragraph>
            © {new Date().getFullYear()}, Built with
            {` `}
            <a href="https://www.gatsbyjs.org">Gatsby</a><br/>
            Was made with &#x1F49A; by 최 준원
          </Paragraph>
        </footer>
      </Wrapper>
    </ThemeProvider>
  )
}
```
