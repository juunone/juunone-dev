---
title: "브라우저 동작 원리."
date: 2019-02-06 10:00:00
category: web
thumbnail: { thumbnailSrc }
draft: false
---

웹사이트는 어떻게 보여줄까?
웹개발자들 이라면 한번쯤 들어봤을 브라우저 렌더링 과정을<br />
좀더 자세히 다뤄보고자 여러 문서들을 기반으로 포스팅을 작성하게됐다.

---

## 브라우저의 렌더링 과정

렌더링은 화면에 컨텐츠를 그리는 과정으로, 브라우저들은 각자의 렌더링 엔진으로 이를 구현했다.
크롬과 사파리는 "Webkit 엔진"을 사용하고, 파이어폭스는 "Gecko 엔진"을 사용한다.

**브라우저 렌더링 과정**<br />
![브라우저렌더링](https://user-images.githubusercontent.com/35126809/90095425-90df1680-dd6b-11ea-9c27-5f9a23359c65.png)

출처 : [http://www.phpied.com/rendering-repaint-reflowrelayout-restyle/](http://www.phpied.com/rendering-repaint-reflowrelayout-restyle/)

1. 불러오기(Load)<br />
   로더(Loader)가 서버로부터 전달 받는 리소스 스트림을 읽는 과정.
   읽으면서 어떤 파일인지, 데이터인지 파일을 다운로드할 것인지 등을 결정한다.

2. 파싱 (Parsing)<br />
   렌더링 엔진은 HTML/XML 문서를 파싱하고 "콘텐츠 트리" 내부에서 태그를 DOM 노드로 변환하고,<br />
   외부 CSS 파일을 포함하여 스타일 요소도 파싱한다.  
   DOM(Document Object Modal), CSSOM(CSS Object Model)을 생성한다.  

   > Bytes < Characters < Tokens < Nodes < DOM 순서로 생성한다.

    ![full-process](https://user-images.githubusercontent.com/35126809/90095244-072f4900-dd6b-11ea-9a7d-d2eb219c3399.png)
    [출처 full-process](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/constructing-the-object-model?hl=ko)

3. 렌더링 트리 만들기<br />
   DOM 트리는 내용을 저장하는 트리로 자바스크립트에서 접근가능한 DOM객체를 쓸 때 이용하고,<br />
   파싱해서 얻은 스타일 정보와 HTML 표시 규칙은 ['렌더 트리'](https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/)라고 부르는 또 다른 트리를 생성한다.<br />
   ['렌더 트리'](https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/)는 색상과 면적 같은 시각적 속성 정보를 가지고 있다.<br />
   (렌더 트리 생성시 필요없는 head, title, body태그등이 없고 display:none 처럼 DOM에 있지만 여과가 필요한 부분들을 제외해준다)

4. Layout:렌더 트리 배치(Reflow)<br />
   생성된 렌더 트리에서는 크기 위치등이 없기 때문에 각 노드가 화면에 정확한 위치에 표시되도록 배치하고 과정이다.<br />
   _Webkit : layout , Gecko : reflow 라고 부른다_

5. Paint:그리기(Repaint)<br />
   배치된 노드들을 visibility, outline, background-color, color와 같이 시각적 속성에 해당하는 형상을 만들어 내는 그리기 과정이다.

- 렌더링 엔진은 순차적으로 수행하지 않고 점진적으로 수행한다. 기다리는 동시에 받은 내용의 일부를 먼저 화면에 표시하는 것이다.<br />
- TABLE의 경우는 기본적으로 점진적 렌더링이 적용되지 않는다. (table-layout:fixed일 경우에만 점진적 렌더링이 적용)<br />
- 그래서 table-layout:auto 보다 table-layout:fixed가 성능에 더 좋다.

**[Webkit 엔진 동작과정]**
![webkit](https://user-images.githubusercontent.com/35126809/90095491-c421a580-dd6b-11ea-9fd7-3c83e36e3913.png)

**[Gecko 엔진 동작과정]**
![gecko](https://user-images.githubusercontent.com/35126809/90095497-cc79e080-dd6b-11ea-971c-a891d0c9e55b.png)

출처 : [https://d2.naver.com/helloworld/59361](https://d2.naver.com/helloworld/59361)

### CSS, Javascript 의 HTML 문서내 위치

- **CSS는 <head></head>태그 사이**

문서를 파싱해서 DOM 노드로 변환을 해도 스타일 정보가 없으면 렌더 트리를 생성 할 수가 없다.<br />
스타일 정보를 빨리 취득하기 위해 <head></head>태그 사이에 놓는것이 렌더 트리 생성시점을 앞당겨 준다.

- **Javascript는 </body>태그 바로 위**

자바스크립트 파일이 <head>내에 위치하게 되면 파서가 파싱을 멈추고 스크립트파일 읽는다.<br />
스크립트파일이 많고 파일이 크면 읽는 시간이 오래걸리고 그만큼 렌더링이 늦어져 엔드 유저에겐 웹페이지 자체가 느리다고 판단 될 수 있다.
또한 스크립트 파일내 에러가 존재할 경우 렌더후 동작이 안되는 결과를 불러올수도 있다.<br />
그래서 </body> 태그 위에 스크립트 파일을 모아둔다.

### Javascript async, defer 속성

**[async, defer 비교]**
![async, defer 비교](https://user-images.githubusercontent.com/35126809/52480792-0b1b6180-2bf0-11e9-88b6-5079c86ab3e3.png)
출처 : [https://www.growingwiththeweb.com/2014/02/async-vs-defer-attributes.html](https://www.growingwiththeweb.com/2014/02/async-vs-defer-attributes.html)

- async

```html
<script async src="script.js">
```

태그 <head>내에 자바스크립트 파일을 놓게 될경우 두가지 속성을 줄 수 있는데,<br />
async 속성은 브라우저에 스크립트 파일이 비동기적으로 실행될 수 있음을 나타내기 위해 사용된다.<br />
parser는 스크립트에 도달한 지점에서 스크립트를 가져오고 실행하기 위해 일시 중지 할 필요가 없다. <br />

**그러나 다운로드가 완료되면 스크립트가 실행될 수 있도록 파싱이 일시 중지 된다.**

- defer

```html
<script defer src="script.js">
```

defer 속성은 파싱이 완료된 후 스크립트 파일을 실행하도록 브라우저에 지시한다.<br />
비동기로 로드된 스크립트와 마찬가지로, 파싱이 실행되는 동안 파일을 다운로드 할 수 있다. <br />
그러나 **파싱이 완료되기 전에 스크립트 다운로드가 완료 되더라도 파싱이 완료 될 때까지 스크립트는 실행되지 않는다.**<br />
또한, async와는 다르게 호출된 순서대로 실행된다.

- 지원범위

|       |     IE     |  Chrome   |   Firefox   |  Safari   |     IOS     |  Android  |
| ----- | :--------: | :-------: | :---------: | :-------: | :---------: | :-------: |
| async | 10버전이상 | 8버전이상 | 3.6버전이상 | 5버전이상 | 5.1버전이상 | 3버전이상 |
| defer |  모두지원  | 8버전이상 | 3.6버전이상 | 5버전이상 | 5.1버전이상 | 3버전이상 |
