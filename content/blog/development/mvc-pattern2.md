---
title: MVC 디자인 패턴
date: 2020-09-27 14:09:39
category: development
thumbnail: { thumbnailSrc }
draft: false
---


# 참고

- [MDN](https://developer.mozilla.org/ko/docs/Glossary/MVC)
- [마기 기술 블로그](https://magi82.github.io/android-mvc-mvp-mvvm/)

# MVC ?

![mvc.png](https://user-images.githubusercontent.com/35126809/94356951-66ed7380-00cf-11eb-9566-fa5a4857a87d.png)

그림 출처: MDN

MVC 는 소프트웨어 에서 사용 되는  디자인 패턴이다.

어플리케이션 에서 비즈니스 로직을 분리해서 사이드 이펙트 없이 하나의 소프트웨어를 만드는 방법론이다.

아래 예시를 설명하기 쉽게 자주 가는 웹 쇼핑몰 하나를 생각해보자.

# Model

모델은 어플리케이션 내의 모든 정보를 담고 있는 집합소 같은 개념이다.

쇼핑몰에서 상품에 대한 정보, 가입한 회원에 대한 정보 등이 모두 저장 관리 되고 있는 곳이 `Model` 이다.

# View

화면에 보이는 모든 요소들을 말한다.

회원가입 화면의 이름 주소 등을 입력하는 폼, 버튼 또는 장바구니 영역의 모든 버튼과 글자 및 인풋들.

**참고로 View는 Controller를 모른다.**

# Controller

Controller는 사용자로부터 입력을 받는다.

사용자로부터 받은 입력에 대한 응답으로 Model,  View 를 업데이트 시키는 영역.

예를 들어 장바구니 버튼을 클릭 했을 때 제품의 수량을 올려줘야 하면 Model의 데이터를 갱신 하고 불러오고,

Model은 갱신된 데이터를 View로 전달해 보여 준다.

Controller 와 View는 1:n 의 관계가 된다.

### View가 갱신되는 방법

- View가 Model을 다이렉트로 갱신
- Model에서 View에게 갱신된 데이터를 전달

# 단점

- View 와 Model은 서로 의존적이다.
- 대규모 어플리케이션 일 수록 서로의 의존도가 높아지고 유지 보수도 힘들어진다.