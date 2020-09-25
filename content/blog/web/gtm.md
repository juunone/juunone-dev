---
title: "Google tag manager 태깅하기"
date: 2020-03-15 15:00:00
category: web
thumbnail: { thumbnailSrc }
draft: false
---

프로젝트 내부에 GTM을 설치하기로했다.  
여러 채널에 대한 태깅관리를 손쉽고 유지보수 비용이 적게 들어가는
분석도구로 구글 태그매니저를 이용하기로 결정했다.

## 참조
[태그 관리자](https://support.google.com/tagmanager/answer/3281060?hl=ko&ref_topic=3281056)

## 태그란?

디지털 마케팅 및 분석에 활용되는 일종의 도구다.  
자바스크립트를 이용해 만들어진 코드를 통해 수집된 데이터를
써드파티로 전달해 대시보드같은 정보를 분석 및 활용하기 위해 사용된다.  
구글 에널리틱스, 페이스북 픽셀, 네이버 애널리틱스 처럼 각 채널에서 사용하는 명칭이 조금씩 상이하다.  
e.g. `페이스북 픽셀`은 1x1 이미지태그를 이용해 파라미터를 활용해가며, 데이터를 수집해서 픽셀이라고 부른다.

## GTM (Google tag manager) 란?

태그는 웹사이트 또는 모바일 앱에 제품을 통합하는 데 도움이 되도록 애널리틱스, 마케팅 또는 지원 제공업체가 제공하는 코드 세그먼트입니다.  
Google 태그 관리자를 이용하는 경우에는 프로젝트에 이러한 태그를 직접 추가하지 않아도 됩니다.  
대신 태그 관리자의 사용자 인터페이스에서 태그를 구성 및 게시하고 태그를 실행할 방법을 지정하면 됩니다.

## 컨테이너 태그?

![컨테이너태그](https://user-images.githubusercontent.com/58495926/77602681-6540c880-6f51-11ea-8b8f-d3b77cdf6138.png)

태깅을 시작하기에 앞서 위와 같이 `컨테이너 태그` 라는걸 소스코드 내부에 심어줘야한다.  
두개의 스니펫에 `head` `body` 에 각각 심는 태그들이 존재한다.  
해당 스크립트를 이용해 태깅 및 액션등을 수집할수 있는것이다.

> ReactJS를 사용한다면 [react-gtm-module](https://www.npmjs.com/package/react-gtm-module) npm 모듈을 사용해 설치할 수 있다.

## 구조

구조는 간단하게 표현하자면 아래와 같이 되어있다.  
태그,트리거,변수는 한세트라고 보면, 트리거는 1:N 관계를 유지하며, 태그에 바인딩된다.

```
|-- Workspace
    |-- Tag
        |-- Trigger
        |-- Variables
```

## 태그
웹사이트 내에서 특정 트리거의 조건에 따라 실행하고자 하는 태그들이다.  
e.g. facebook 'ViewContent', 'Purchase' 등 사이트에서 정보를 수집하고 외부채널에 전송한다.
![태그](https://user-images.githubusercontent.com/58495926/77723066-b70d4f80-7032-11ea-9376-28b002303937.png)

## 트리거
태그가 실행되는 조건, 즉 참 또는 거짓을 구분하고 참일때 실행한다.  
태그가 실행되기위해선 무조건 1개이상의 트리거가 필수다.
![트리거](https://user-images.githubusercontent.com/58495926/77724096-72cf7e80-7035-11ea-960e-cbeaf0f8ba91.png)

## 변수
트리거와 태그에서 모두사용되며, 트리거에서 실행 조건을 설정할때 사용된다. e.g. 제품금액
![변수](https://user-images.githubusercontent.com/58495926/77723148-f63ba080-7032-11ea-9e60-9588f7b47ca3.png)

## 데이터레이어

태그나 트리거외에 웹사이트에서 GTM을 통해 데이터를 전달할때 자바스크립트 객체를 의미한다.  
태그를 구성할때 트리거의 필터나 변수에 담아서 태그에서 외부채널에 데이터를 전달할때도 사용된다.

[Developer guide](https://developers.google.com/tag-manager/devguide#events)

### 잘못된 방법
```html
<!-- Google Tag Manager -->
...
<!-- End Google Tag Manager -->
<script>
  dataLayer = [{
    'pageCategory': 'signup',
    'visitorType': 'high-value'
  }];
</script>
```

### 올바른 방법
```html
<script>
  window.dataLayer = window.dataLayer || [];
  window.dataLayer = [{
    'pageCategory': 'signup',
    'visitorType': 'high-value'
  }];
</script>
<!-- Google Tag Manager -->
...
<!-- End Google Tag Manager -->
```
> 태그매니저를 소스에 삽입하기전 dataLayer 변수를 초기화하고 그후에 import 해주는 방법을 추천한다.

### 데이터 추가방법  
아래와 같이 `dataLayer.push` 메소드로 필요한 데이터를 밀어 넣고 태그에서 사용할 수 있다.
```js
dataLayer.push({'event': 'event_name'});
```

### 미리보기

GTM의 어드민에서 미리보기 기능을 사용하게되면, GTM이 설치된 웹에서 디버깅이 가능하다.  
설정된 태그 및 트리거 발생유무와 변수, 데이터레이어에 대한 정보도 확인 가능하다.  

![미리보기](https://user-images.githubusercontent.com/58495926/77878981-875d8200-7294-11ea-8a49-348e7ea11e28.png)
![미리보기 데이터](https://user-images.githubusercontent.com/58495926/77879110-afe57c00-7294-11ea-85c9-eb00f9e41f2a.png)

### Hotjar 추가

핫자라는 히트맵이나 레코딩을 제공해주는 툴을 추가로 태깅했다.  
구글 태그매니저 에서 태그 추가 영역에 보면 추천태그에 `Hotjar Tracking Code`라는 타이틀로 존재한다.
먼저 Hotja에 가입하고 발급받은 `Hotjar Site ID` 아이디만 넣어주면 init 할 수 있다.  
트리거로는 `All pages 페이지뷰`를 넣고 전체페이지 레코딩이 가능하게 해주면 기본 세팅은 끝난다.

![hotjar gtm](https://user-images.githubusercontent.com/58495926/82853909-ee6c7f80-9f41-11ea-87d8-b4a30c3f9a74.png)

- Hotjar 사이트 아이디는 `https://insights.hotjar.com/sites/0000000/dashboard`의 url에서 7자리 숫자에서 확일할 수 있고,  
  대시보드 오른쪽 상단의 Tracking 버튼을 통해서도 확인 가능하다.


hotjar sdk가 정상적으로 삽입된 웹사이트에서는 대시보드 상단의 Tracking 버튼을 통해 확인 해볼 수 있다.  
`Verify installation`이라는 버튼을 클릭하면 자신의 웹사이트에 아래 그림과 같은 뱃지가 붙으면 정상적으로 완료된 것이다.

![hotjar verify](https://user-images.githubusercontent.com/58495926/82854025-3b505600-9f42-11ea-8969-ce1b31edfef8.png)

### Hotjar 커스텀 태깅

hotjar도 페이스북이나 다른 채널처럼 추가로 원하는 태깅을 할 수 있다.  
[Hotjar docs](https://help.hotjar.com/hc/en-us/categories/360002046454)에서 보면 
특정 트리거나 이벤트를 통해 레코딩을 시작할 수 있는데 아래와 같이 트리거라는 이벤트명을 걸고 태깅을 하면  
해당 태깅이 발생했을 시점부터 레코딩이 시작된다.
하지만 한가지 문제는 `This feature is only available on the Plus and Business plan.` 에서만 가능하다.  
즉 돈내라는 소리... 결제하고 써도 충분히 좋은 툴이니 가치가 있다고 생각한다.

```js
<script>
hj('trigger', 'ViewStep',{
  user_name:{{유저 닉네임}},
  user_email:{{유저 이메일}},
});
</script>
```







