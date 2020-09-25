---
title: "브라우저에서 주소창에 URL을 입력하면 생기는일"
date: 2020-07-15 14:00:00
category: web
thumbnail: { thumbnailSrc }
draft: false
---

- 참조
  - [aws route53](https://aws.amazon.com/ko/route53/what-is-dns/)
  - [잉구 블로그](https://wangin9.tistory.com/entry/%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80%EC%97%90-url-%EC%9E%85%EB%A0%A5-%ED%9B%84-%EC%9D%BC%EC%96%B4%EB%82%98%EB%8A%94-%EC%9D%BC%EB%93%A4-intro?category=827054)
  - [alex/what-happens-when](https://github.com/alex/what-happens-when)

## 브라우저의 역할?

1. e.g. 크롬을 실행한다.
2. 주소창에 URL 입력한다.
3. URL의 DNS(Domain Name Sever)에서 사이트 컨텐츠를 받아와 화면에 그린다

여기서 위의 3번째 내용에 대해 설명하고자 한다.

## Browser 주소창에 URL을 입력하면 생기는일?

참고

- 비디오 애니메이션 통해서 브라우저에 URL을 입력받으면 생기는일 바로가기
[![video](https://img.youtube.com/vi/2ZUxoi7YNgs/0.jpg)](https://youtu.be/2ZUxoi7YNgs)

-  [HTTPS 작동 방식을 애니메이션으로 설명한 사이트 바로가기](https://howhttps.works/ko/)

- 브라우저에 URL 입력시 작동 방식 한눈에 보기
![브라우저에 URL 입력시 작동 방식 한눈에 보기](https://user-images.githubusercontent.com/35126809/94227775-4e962100-ff36-11ea-8900-4ffb29ad84f1.jpeg)
출처: https://twitter.com/kamranahmedse/status/1297131414190776320

- - - 

![search](https://user-images.githubusercontent.com/35126809/90083636-1522a100-dd4e-11ea-9d1a-bbcb741ef838.png)

1. 브라우저에서 위와 같은 주소창을 통해서 사용자의 URL 입력을 받게 된다.

2. 브라우저의 URL 파싱
  - <code><span style="color:green">http</span>://<span style="color:red">www.google.com</span><span style="color:orange">:80</span><span style="color:yellow">/dashboard</span><span style="color:purple">?date=lifetime&type=campaign</span><span style="color:skyblue">#top</span></code>
    - green: 프로토콜, 네트워크상에서 데이터 교환하기 위한 방법 `http:` `https:` 을 사용.
    - red: 도메인, 사용자가 외우기어려운 ip 대신 식별하기 쉬운 도메인을 사용한다.
    - oragne: 포트번호, http:80 , https:443을 사용한다. 생략가능
    - yellow: 경로, 웹서버의 리소스 경로 즉, 파일이 위치한 경로
    - purple: 파라미터, `&` 키워드로 추가입력가능 및 key,value로 이루어짐.
    - blue: fragment, 앵커, 식별자의 개념으로 서버에 요청시에 제외된다.

3. HSTS(HTTP Strict Transport Security) 목록 확인 및 요청 처리
  - HTTP Strict-Transport-Security response header (종종 HSTS 로 약칭) 는 HTTP 대신 HTTPS만을 사용하여 통신해야한다고 웹사이트가 브라우저에 알리는 보안 기능. [출처 MDN](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/Strict-Transport-Security)

4. DNS(Domain Name Server)에서 IP 주소 가져오기.
  - DNS란 사람이 읽을 수 있는 주소를 컴퓨터가 인식 할 수 있는 IP주소로 변환
  - https://www.google.com를 IP주소로 변환하는 작업
  - 사람이 읽을 수 있는 도메인 이름을 컴퓨터가 읽을 수 있는 IP 주소로 변환
  1. Browser DNS Cache
    - 브라우저에 도메인이 캐시되어있는지 확인한다.
  2. OS DNS Cache
    - 브라우저에서 못찾으면 OS에 저장된 hosts파일에서 도메인이 있는지 확인하다.
  3. DNS Server
    - 위 둘다 존재하지 않으면 Root DNS부터 조회를 하여 결과(참조 이미지1)를 가져옵니다.
    ![dns](https://user-images.githubusercontent.com/58495926/73413445-0d218780-434f-11ea-9471-d10666579222.png)

5. IP 주소를 ARP(Address Resolution Protocol)를 통해 MAC주소로 변환
  - IP: 내 PC를 찾기 위해 필요한 주소. 기기마다 고유한값이 아닌 바뀔 수 있는 유동적인 값.about
  - MAC: 하드웨어 자체에 부여된 고유한 식별 가능한 번호.

6. 서버와 TCP socket 연결  
  서버 IP주소로 TCP 3-way-handshake를 통해 통신을 한다.  
  **https는 TLS handshake 추가된다.**
  ![tcp handshake](https://user-images.githubusercontent.com/35126809/90088477-ffb37400-dd59-11ea-897c-3c0668d7040f.png)
  [출처 3-way-handshake](https://www.johnpfernandes.com/2018/12/08/the-tcp-3-way-handshake/)

7. 서버에 Resource 요청.
  서버에서 받은 리소스를 가지고 browser에서 rendering을 해준다.

이후의 과정이 궁금하다면 [브라우저 렌더링 과정](https://juunone.github.io/browser/) 참고.

> 구글에 제목대로 검색하면 무수히 많은 글이 나오지만 내가 기록하는 방식으로 다시 한번 남기기 위해 작성한다.  
> 호스팅: 웹 페이지의 구성 파일들이 호스트 서버에 저장되어 있다가 사용자의 요청이 오면 언제든지 응답을줌.