---
title: "Fetch API 사용법"
date: 2018-05-12 14:00:00
category: web
thumbnail: { thumbnailSrc }
draft: false
---

## **REST API Connect**

그동안의 프로젝트를 돌아보면 우리는 jquery 기반의
*AJAX*를 사용해왔다.<br />
물론 편리하고 보편화되어있고 지원되는 optional 도 많지만
현재 React app에서 최대한 jquery 영역을 좁히고있는 와중에<br />
새로운 API Connenctor 인 [Fetch API](https://developer.mozilla.org/ko/docs/Web/API/Fetch_API/Fetch%EC%9D%98_%EC%82%AC%EC%9A%A9%EB%B2%95)를 도입하게되었다.

### Fetch API

위 링크를 참조로 작성한 내용입니다.

- Fetch API를 이용하면 Request나 Response와 같은 HTTP의 파이프라인을 구성하는 요소를 조작하는것이 가능합니다. 또한 fetch() 메서드를 이용하는 것으로 비동기 네트워크 통신을 알기쉽게 기술할 수 있습니다.

- jquery.ajax() 와 차이점
  - fetch()로 부터 반환되는 Promise 객체는 HTTP error 상태를 reject하지 않습니다. 대신 ok 상태가 false인 resolve가 반환되며, 네트워크 장애나 요청이 완료되지 못한 상태에는 reject가 반환됩니다.
  - 보통 fetch는 쿠키를 보내거나 받지 않습니다. 사이트에서 사용자 세션을 유지 관리해야하는 경우 인증되지 않는 요청이 발생합니다. 쿠키를 전송하기 위해서는 자격증명(credentials) 옵션을 반드시 설정해야 합니다.

---

### Basic

기본적인 fetch 작성법

```javascript
fetch("https://jsonplaceholder.typicode.com/todos/1")
  .then(response => response.json())
  .then(json => console.log(json));

  response : {
  "userId": 1,
  "id": 1,
  "title": "delectus aut autem",
  "completed": false
}
```

### Headers

Headers 인터페이스에서 Headers() 생성자를 사용해 헤더 객체를 생성할 수 있습니다.

```javascript
const content = "Hello World!";
const awesomeHeaders = new Headers();
awesomeHeaders.append("Content-Type", "application/json");
awesomeHeaders.append("Content-Length", content.length.toString());

//객체 리터럴을 생성자에 전달하는것으로 생성할수도 있습니다.
const awesomeHeaders = new Headers({
  "Content-Type": "application/json",
  "Content-Length": content.length.toString()
});
```

### Body

Request, Resposne 둘다 Body를 가지고 있습니다. body는 아래에서 기술한 타입들 중 하나로 설정할 수 있습니다.

- ArrayBuffer
- ArrayBufferView (Uint8Array같은 TypedArray)
- Blob/File
- 문자열
- URLSearchParams
- FormData

Body를 다루기위해서 제공되는 Method가 있다.

- arrayBuffer()
- blob()
- json()
- text()
- formData()

```javascript
fetch("https://jsonplaceholder.typicode.com/posts", {
  method: "POST",
  body: JSON.stringify({
    title: "foo",
    body: "bar",
    userId: 1
  }),
  headers: {
    "Content-type": "application/json; charset=UTF-8"
  }
})
  .then(response => response.json())
  .then(json => console.log(json));
```

### Request

두번째 파라미터를 적용하는것도 가능합니다. 다수의 설정을 컨트롤할 수 있는 초기화 옵션입니다.
아래와 같이 두번째 인자에 들어갈수 있는 옵션들입니다.

- method
- headers
- body
- mode
- cache
- credentials
- redirect
- referrer
- integrity

```javascript
const init = {
  method: "GET",
  mode: "cors", //The mode you want to use for the request, e.g., cors, no-cors, or same-origin.
  cache: "default" // The cache mode you want to use for the request.
};

fetch("train.jpg", init)
  .then(function(response) {
    return response.blob();
  })
  .then(function(myBlob) {
    const objectURL = URL.createObjectURL(myBlob);
    myImage.src = objectURL;
  });
```

### Query parameters

쿼리 매개 변수도 지원됩니다.

```javascript
fetch("https://jsonplaceholder.typicode.com/posts?userId=1")
  .then(response => response.json())
  .then(json => console.log(json));
```

### 마치며

jQuery를 굳이 사용할 필요가 없다면 Ajax 없이도 충분히 API 통신하기에 부족함이 없다.<br />
