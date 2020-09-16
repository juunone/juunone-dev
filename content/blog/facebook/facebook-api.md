---
title: "페이스북 마케팅 API 사용기"
date: 2017-10-21 10:00:40
category: facebook
thumbnail: { thumbnailSrc }
draft: false
---

## 페이스북 개발자앱 생성 및 토큰 발급

SDK 생성 및 토큰을 발급 받기위해서는 [페이스북 개발자센터-앱](https://developers.facebook.com/apps/) 에서<br />
앱을 생성한 후 해당 앱을 통해 SDK 생성, API 버전 관리, 화이트리스트추가, 도메인관리, 비즈니스 관리자구성 등을 할 수 있다.

![개발자앱](https://user-images.githubusercontent.com/35126809/64483771-5c9dd480-d243-11e9-8aee-4e7d38bbb728.jpg "개발자앱")

그림)개발자앱

---

### 앱 제품구성

개발자앱에서는 체크된 제품을 사용중이다.<br />
_(더 많은 제품이 존재할 수 있다.)_

![제품구성](https://user-images.githubusercontent.com/35126809/64483773-5dcf0180-d243-11e9-8bc6-3c8f79e0fa9a.png "구성")

---

## 마케팅 API

다양한 제품중 **마케팅 API** 대해 ~~자세히~~ 그냥 다뤄보겠다.

### 구성

- 대시보드
  - API 호출/오류/오류율에 대한 인사이트
  - 마케팅 API 사용정보
  - 마케팅 API 오류 및 호출 통계
- 빠른 시작
  - 마케팅 API 가이드 및 튜토리얼 실습 안내
    - 페이지 홍보하기
    - 광고 보고서 만들기
    - 다이내믹 광고 설정
    - 맞춤 타겟 만들기
    - 광고 규칙 만들기
- 도구

  - 액세스 토큰 받기
  - 샌드박스 광고 계정 관리

- 설정
  - 비즈니스 관리자 구성
  - 광고 API 액세스 범위
  - 마케팅 API 앱 검수
    - 앱 검수 항목을 통해서 개발자앱 > 제품(마케팅 API) > 앱 검수 요청이 가능하다

---

## 그래프 API 탐색기

페이스북에서 제공하는 API 탐색기다.<br />
Postman 처럼 마케팅 API를 토큰을 가지고 **GET,POST,DELETE** 메소드 모두 확인가능하다.<br />
현재(2018.12.13 기준) 그래프 API 버전 **_v2.10 ~ v3.2_** 까지 이용가능한걸로 확인된다.<br />
아래 링크로 이동가능하다.

- [기존 그래프 탐색기](https://developers.facebook.com/tools/explorer/)
- [베타버전 새로운 그래프 탐색기](https://developers.facebook.com/tools/explorer/?classic=0)

### 토큰 발급

- 탐색기에 접근후 토큰받기 버튼을 토클하게되면 등록되어있는 개발자앱을 선택후에 권한선택을 한후 토큰을 발급 받을 수 있다.

![권한](https://user-images.githubusercontent.com/35126809/64483774-5f002e80-d243-11e9-8ad3-df5deacf1023.png "권한선택")

그림)권한선택

---

- 발급받은 액세스토큰의 왼쪽 느낌표 마크를 클릭하게되면 해당 토큰 정보조회가 가능하다.

![토큰조회](https://user-images.githubusercontent.com/35126809/64483783-a4bcf700-d243-11e9-96a1-1a52ba9a545a.jpg "토큰조회")

그림)토큰조회

---

### 그래프 사용법

메소드의 종류에는 총 3가지가 존재한다.

- `GET`

![get.method](https://user-images.githubusercontent.com/35126809/64483784-a4bcf700-d243-11e9-8b6b-f679dab4ce49.jpg "getmethod")

그림의 상단을 보면 메소드/API버전/파라미터 로 구성되어있다.<br />
메소드 및 버전은 선택 및 변경이 가능하다.<br />
파라미터의 경우 그림의 왼쪽 Node 부분을 참조하면 된다.<br />
여기서 말하는 `Node`란 API의 엔트리를 지칭하며, *필드 검색*을 통해 현재 `Node` 에서 지원가능한 파라미터를 조회할 수 있다.

> 그림의 내용을 간단하게 풀어보면 아래와 같다.

```javascript
path : "https://graph.facebook.com/me"
method : "GET"
version : "v3.2"
params : {
    access_token:"토큰",
    fields : "id,name"
}

//결과
response = {
    "id": "유저아이디", //String
    "name": "유저이름" //String
}

```

---

> 위와 같이 `Node` & `필드` 에 대한 이해가 되었다면 심화학습을 해보자.<br />
> 광고계정을 `Node` 로 설정하고 `필드`에 **camapaigns(adsets(limit,name)),id,name** 을 그래프를 통해 호출해보겠다.

```javascript
path : "https://graph.facebook.com/act_광고계정아이디"
method : "GET"
version : "v3.2"
params : {
    access_token:"토큰",
    fields:"campaigns.limit(1){adsets{name,id},name,id},id,name"
}

//결과
response = {
  "campaigns": {
    "data": [
      {
        "adsets": {
          "data": [
            {
              "name": "광고세트 이름", //String
              "id": "광고세트 아이디" //String
            }
          ],
          "paging": {
            "cursors": {
              "before": "",
              "after": ""
            }
          }
        },
        "name": "캠페인 이름", //String
        "id": "캠페인 아이디" //String
      }
    ],
    "paging": {
      "cursors": {
        "before": "",
        "after": ""
      },
      "next": "리미트에 걸린 다음 결과값 cURL 주소" //String
    }
  },
  "id": "광고계정 아이디", //String
  "name": "광고계정 이름" //STring
}
```

이해하면 간단하다.~~(아마도)~~<br />
내가 가져오고싶은건 아래와 같다.

- 광고계정 아이디,이름
- 캠페인 아이디,이름
- 광고세트 아이디,이름

광고계정은 캠페인과 광고세트를 포함하고있으므로 `Node = act_광고계정아이디` 가 되고,<br />
`필드 = campaigns.limit(1){adsets{name,id},name,id},id,name` 와 같이 된다.<br />
여기서 주목할건 각 파라미터마다 `limit` 을 부여할 수 있으며,<br />
**객체 구조를 가지고 각 엔트리에 필드들을 추가하며 하위 요소들까지 가져올 수 있다.**<br />
_campaigns.limit(1)_ 가운데 점은 캠패인에 _limit_ 를 부여하며 1개의 캠페인만 호출중이고,<br />
_{adsets{name,id},name,id_ 는 캠페인안에 _adset_ 이라는 객체의 _name,id_ 및 *캠페인의 ,name,id*도 가져온다는걸 의미한다.<br />
마지막 _,id,name_ 는 현재 최상위 `Node` 에서 설정된 광고계정의 *id,name*을 의미한다 **(campaigns 와 같은 레벨이다)**.<br />
추가로 캠페인 레벨에 걸려있는 리미트로 인해 `next` 라는 키가 포함된다.<br />
예를들어 총 3개의 캠페인이 존재하지만 1개의 캠페인만 조회를 한경우 나머지 2개의 캠페인은 `next` 의 *value = cURL*을 통해 조회 가능하다.

---

- `POST & DELETE`

![포스트및삭제](https://user-images.githubusercontent.com/35126809/64483785-a4bcf700-d243-11e9-88d7-710a6fee2919.jpg "postdelete")

GET 메소드를 이해했다면 POST,DELETE 는 1초면된다.<br />
GET 과 다른점은 **필드**가 필요없다.<br />
`Node`에 캠페인 아이디를 가지고 `파라미터` 는 _status = PAUSED_ 를 추가했다.

> 그림의 내용을 간단하게 풀어보면 아래와 같다.

```javascript
path : "https://graph.facebook.com/캠페인아이디"
method : "POST"
version : "v3.2"
params : {
    access_token:"토큰",
    status : "PAUSED"
}

//결과
response = {
    "success" : true //Boolean
}

```

---

### 탐색기 꿀팁

![팁](https://user-images.githubusercontent.com/35126809/64483786-a4bcf700-d243-11e9-8422-c25f58b7399a.png "팁")

탐색기 하단에 보면 위그림과 같이 **디버그 정보 복사, 코드 받기, 세션 저장**이 존재한다.

- 디버그 정보 복사 : 현재 탐색기에서 사용 로그를 보여준다.

```javascript
==== Query
curl -i -X POST \
 "https://graph.facebook.com/v3.2/?access_token=<access token sanitized>"
==== Access Token Info
{
  "perms": [
    "email",
    "read_insights",
    "read_audience_network_insights",
    "manage_pages",
    "pages_manage_cta",
    "pages_manage_instant_articles",
    "pages_show_list",
    "publish_pages",
    "ads_management",
    "ads_read",
    "business_management",
    "instagram_basic",
    "instagram_manage_comments",
    "instagram_manage_insights",
    "public_profile"
  ],
  "user_id": "",
  "app_id": ""
}
==== Parameters
Query Parameters{}

- POST Parameters
{
    "status": "PAUSED"
}

==== Response
{
    "success": true
}
```

- 코드 받기 : 현재 사용한 탐색기의 Androis SDK , iOS SDK, Javascript SDK, PHP SDK, cURL 코드 형태로 받을 수 있다.
- 세션 저장 : 현재 세션을 참조용이나 디버깅 목적으로 저장할 수 있습니다.

---

## 마치며

위 글은 그래프 API 탐색기에서 간단한 사용방법을 알아보았다.<br />
페이스북 마케팅 API는 **그래프 API 탐색기**를 통해 무엇이든 구현이 가능하다.<br />
[마케팅 API 문서](https://developers.facebook.com/docs/marketing-apis/)에 접속하면 지문으로 된
방대한 양의 페이스북 API 문서 가이드가 존재한다.<br />
위 페이스북 문서와 함께 탐색기를 사용하면 좀더 디테일한 작업이 가능할것이다.<br />
**_마지막으로 페이스북의 API 버전을 믿지마라. 언제든지 deprecated 될 수 있다._** 🤯
