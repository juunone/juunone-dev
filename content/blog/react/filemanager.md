---
title: "React File Manager 프로젝트 회고록"
date: 2018-07-03 12:00:00
category: react
thumbnail: { thumbnailSrc }
draft: false
---

# File-Manager 개발정의서

## **들어가며**

본 문서는 인수인계 목적이 아닌
개발이 완료된 제품에 대한 이해를 돕기위해 제작된 개발 정의서입니다.<br />
부디 도움이 되길 바랄 뿐입니다. 그럼 스타뜨!

## **배경**

---

DCE내에서 파일 업로드 및 관리를 할수 있는 GUI 화면이 필요했다.

- 확장성을 보유할것
- 외부 프로젝트에서도 사용 가능할것.
- ~~재밌겠다~~

## **문제정의**

---

회사내에서 사이드 프로젝트로 플러그인 형태의 파일을 관리 할 수 있는 컴포넌트가 없다.

- 독립되어있는 프로젝트는 의존성이 높다. (SSO 로그인 등)
- DCE 에서 사용하는 프레임웍과 공존이 힘들다.

## **예상 및 해결**

---

DCE내에 소스코드가 존재할 예정이라 React 프레임웍을 사용하지만<br />
형태는 jquery 플러그인처럼 외부에서 스크립트를 import한 상황에서 함수 혹은 메소드를 입력하면 실행이 가능하게 구현하자.<br />
버전업 될때마다 배포후 자동적으로 사용중인 프로젝트에 적용이 되어야한다.<br />
[참조링크](https://swizec.com/blog/using-react-in-the-real-world/swizec/6710)를 통해 작은 희망을 보았다.

## **기술 스펙**

| name                    | version | dependencies    | role        |
| ----------------------- | ------- | --------------- | ----------- |
| react                   | ^16.2.0 | dependencies    | framework   |
| jquery                  | ^3.2.1  | dependencies    | plugin      |
| webpack                 | ^3.10.0 | devDependencies | bundle      |
| webpack-dev-server      | ^2.9.7  | devDependencies |             |
| webpack-merge           | ^4.1.1  | devDependencies |             |
| react-copy-to-clipboard | ^5.0.1  | devpendencies   | node_module |
| react-select            | ^1.2.1  | devpendencies   | node_module |

---

최소한의 의존성을 띄고있는것에 중점을 두었으며, 파일매니저 내에서 사용하는 대부분의 기능은<br />
vanilla javascript 혹은 window.method 로 구성되어있다.<br />
ReactDOM.render 를 window.FManager 라는 윈도우 함수 생성후 호출하게 된다.

```javascript
window.FManager = function(el, data) {
  ReactDOM.render(
    <div>
      <Manager opt={data} /> //파일매니저 전체 컴포넌트
    </div>,
    document.getElementById(el)
  );
};
```

## **기술에 대한 의견**

> ## Webpack
>
> - 목적
>   - 수십 혹은 수백개의 html,css,js 파일들의 통합 관리 목적
> - 효과(주관적)
>   - webpack 의 entry 를 여러개로 분리 가능.
>   - 로컬/테스트/리얼 환경별 빌드 관리
>   - Webpack-dev-server 활용한 개발 편의성 증대
> - 장단점
>   - [참조1](https://medium.com/@ljs0705/spa-single-page-app-%EC%97%90%EC%84%9C-webpack%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0-ce7d3f82fe9)
>   - [참조2](https://dev.zzoman.com/2017/09/04/why-do-you-need-to-learn-about-webpack/)

### 파일매니저 javascript export

```javascript
//webpack.config.js 설정의 일부

const FILEUPLOADER_PATH = path.resolve(
  APP_PATH,
  "components/Uploader/render.js"
); //ReactDOM 을 render 해주는 component의 위치

module.exports = {
  entry: {
    filemanager: FILEUPLOADER_PATH //filemanager.js 로 export 시키는 엔트리 생성.
  }
};
```

위와 같이 entry를 분리해서 웹에서 확인해볼경우 <https://도메인/dist/js/filemanager.js> 에서<br />
filemanager.js라는 이름으로 렌더링 컴포넌트가 export 된걸 확인 할 수 있다.<br />
결론적으로 `components/Uploader/render.js` 안에 import 된 컴포넌트들을 묶어 플러그인 형태의 `filemanager.js` 로 export 해주었다.

## **폴더 구조 및 형태**

| name              | path                                                          | shape      | desc                    |
| ----------------- | ------------------------------------------------------------- | ---------- | ----------------------- |
| Render            | /static/file-manager/src/components/Uploader/UppyRender.js    | javascript | ReactDOM render         |
| Manager           | /static/file-manager/src/components/Manager/Manager.js        | javascript | 사용되는 메소드들       |
| ManagerPopup      | /static/file-manager/src/components/Manager/ManagerPopup.js   | javascript | 리스트 및 관리 컴포넌트 |
| ManagerPopup.scss | /static/file-manager/src/components/Manager/ManagerPopup.scss | scss       | 파일매니저 전체 style   |
| Juunpy            | /static/file-manager/src/components/Manager/Juunpy.js         | javascript | 업로드 컴포넌트         |

## **전역 호출 함수**

```javascript
window.FManager; //filemanager.js 에서 선언된 윈도우 함수.

new FManager("manager", {
  //첫번째 인자는 document.getElementById() 에 들어갈 엘리먼트 id
  apiSetup: {
    endpoint: "", //API URL | String
    apiKey: "" //access_token | String
  },
  uploadOpt: {
    restrictions: {
      maxFileSize: 1100000, //개별 파일 용량(환산:bytes) | Number
      maxNumberOfFiles: 5, //최대 업로드 파일갯수 | Number
      allowedFileTypes: ["image/png", "image/jpg", "image/gif", "image/jpeg"] //업로드 가능한 확장자 | Array
    },
    button: {
      style: {
        marginTop: "2px", //String
        marginRight: "4px" //String
      },
      class: "btn btn-sm btn-m-black", //버튼 커스텀 클래스 | String
      text: "Upload files" //버튼 커스텀 텍스트 | String
    },
    strings: {
      //업로더 텍스트 영역
      browse: "찾아보기", //파일 업로드버튼 커스텀 텍스트 | String
      note: "이미지 전용, 1-5 개의 파일, 최대 1MB" //업로드 설명 | String
    },
    plugin: {
      uploadSuccessful(data) {
        console.log("업로드성공", data); //업로드 성공 콜백
      },
      uploadFailed(data) {
        console.log("업로드실패", data); //업로드 실패 콜백
      }
    }
  },
  managerOpt: {
    resetChecked: false, //체크박스 초기화 | Boolean
    multipleCheck: true, //다중 선택여부 | Boolean
    copyButton: "btn btn-m-pink btn-icon-anim btn-circle", //복사하기 버튼 커스텀 클래스 | String
    downloadButton: "btn btn-m-blue btn-icon-anim btn-circle", //다운로드 버튼 커스텀 클래스 | String
    modal: {
      modalTitle: "File management", //모달 타이틀 | String
      buttonTitle: "File manager", //버튼 타이틀 | String
      buttonClass: "btn btn-m-default btn-sm", //버튼 커스텀 클래스 | String
      backdrop: "false" //배경그림자 | String
    },
    plugin: {
      selectFiles(data) {
        console.log("선택된파일", data); //선택된 파일 콜백
      },
      deleteFiles(data) {
        console.log("지워진파일", data); //지워진 파일 콜백
      }
    }
  }
});
```

## **마치며**

![파일매니저1](https://user-images.githubusercontent.com/35126809/64483936-47767500-d246-11e9-921d-08a34e0fc732.png "파일1")
(그림1 : 리스트)

![파일매니저2](https://user-images.githubusercontent.com/35126809/64483937-47767500-d246-11e9-977b-eb81c90e5d68.png "파일2")
(그림2 : 업로드대기)

![파일매니저3](https://user-images.githubusercontent.com/35126809/64483938-480f0b80-d246-11e9-9aa8-51965563632f.png "파일3")
(그림3 : 업로드완료)

![파일매니저4](https://user-images.githubusercontent.com/35126809/64483939-480f0b80-d246-11e9-9cc5-712a74a2cbe4.png "파일4")
(그림4 : 정보)

제품을 개발하며 제일 주안점을 두었던 부분은 **_편의성, 확장성, 범용성_** 이였다.<br />
위 그림과 같이 GUI 보면 사실 간단하지만... 개발하는데는 매우 많은 시간을 투자했다.

- 메소드 하나로 호출이 쉽게 이루어져야한다.
- 어떤 프로젝트든 쉽게 import 가능해야한다.
- 환경에 구애받지 않아야 한다.
- 배포를 하며 기존에 삽입되어있는 코드는 수정이 이루어지지 않아야한다.
