---
title: "MVC, FLUX 패턴"
date: 2018-04-02 22:00:00
category: web
thumbnail: { thumbnailSrc }
draft: true
---

## 참조
- https://velopert.com/

## **Design pattern**

![redux](https://user-images.githubusercontent.com/35126809/64483898-81934700-d245-11e9-8976-7ffbf039472c.png "redux")

### Redux?

컴포넌트들끼리 데이터교류를 쉽고 효율적으로 할수 있게
도와주는 *라이브러리*입니다.

- 리덕스의 원대한 목표는 플럭스를 기반으로 애플리케이션안에서
  **변경된 데이터가 어떻게 흘러가는지 더명확히 표현하도록 해주는 것**입니다.

### Flux?

![flux](https://user-images.githubusercontent.com/35126809/64483899-81934700-d245-11e9-8301-3fc09fe2ff3b.png "flux")

Flux는 일종의 아이디어이다.
라이브러리나 프레임워크가 아닌 일종의 프로그래밍 *방법론*이다.

- Flux 패턴은 react.js 처럼 데이터 흐름이 단방향으로 전달되는 소프트웨어 개발에서 사용된다.
- 이런 추상적인 방법론을 구현한게 Redux입니다.

### MVC?

![mvc](https://user-images.githubusercontent.com/35126809/64483900-822bdd80-d245-11e9-83fc-3977c8df8f65.png "mvc")

MVC 패턴은 _Controller, Model, View_ 이 3가지 개념으로 이뤄져있습니다. 어떠한 Action 이 입력되면, Controller은 Model이 지니고 있는 데이터를 조회하거나 업데이트 하며, 이 변화는 View 에 반영되는 구조입니다.<br />
또한, View에서 Model의 데이터에 접근 할 수도 있습니다.

![mvc app](https://user-images.githubusercontent.com/35126809/64483901-822bdd80-d245-11e9-9a2c-88af8a2b866f.png "lot of app")

작은 어플리케이션에서는 큰 문제없이 작동합니다. 하지만, Model과 View가 늘어난다면 구조가 복잡해집니다.<br />
페이스북과 같은 대규모 애플리케이션에서는 MVC가 너무 빠르게, 너무 복잡해진다는 것이다.<br />
페이스북 개발팀에 따르면 구조가 너무 복잡해진 탓에 새 기능을 추가할 때마다 크고 작은 문제가 생겼으며 코드의 예측이나 테스트가 어려워졌으며 새로운 개발자가 오면 적응하는데만 한참이 걸려서 빠르게 개발할 수가 없었다고합니다.<br />
소프트웨어의 품질을 담보하기가 힘들어졌다는 뜻이죠

### Simple of Flux

![flux](https://user-images.githubusercontent.com/35126809/64483902-822bdd80-d245-11e9-8c6a-fe31f780468c.png "simple flux")

- 시스템에서 Action 을 받았을 때, Dispatcher가 받은 Action들을 통제하여 Store에 있는 데이터를 업데이트합니다<br />
  변동된 데이터가 있으면 View를 리렌더링 하게 됩니다
- View에서 Dispatcher로 Action을 보낼 수도 있다
- View에서 사용자의 입력이 있는 경우 view는 액션을 호출하므로 ​사용자의 입력을 고려하면 위 다이어그램처럼 표현 할 수 있습니다.
- 데이터의 흐름은 언제나 디스패처(Dispatcher)에서 스토어(Store)로, 스토어에서 ​뷰(View)로, 뷰에서 액션(Action)으로 다시 액션에서 디스패처로 흐른다.
- Dispatcher는 작업이 중첩되지 않도록 해줍니다. 즉, 어떤 Action이 Dispatcher를 통하여 Store에 있는 데이터를 처리하고, 그 작업이 끝날 때 까지 다른 Action들을 대기시킵니다.

### Finally Redux

![redux](https://user-images.githubusercontent.com/35126809/64483908-aab3d780-d245-11e9-8516-faf6331c905a.png "redux")

위와 같이 코드가 구성되어있다고 했을때
A 를 거치고 E 를 거치고 G 를 거쳐서 state를 전달해주어야합니다.
하지만 리덕스를 쓴다면 아래 그림과 같이 표현됩니다.

---

![redux store](https://user-images.githubusercontent.com/35126809/64483909-ab4c6e00-d245-11e9-89f7-321269b0619c.png "redux store")

---

![redux store2](https://user-images.githubusercontent.com/35126809/64483910-ab4c6e00-d245-11e9-840f-724466765a7e.png "redux store2")

---

![redux store3](https://user-images.githubusercontent.com/35126809/64483911-ab4c6e00-d245-11e9-886a-87bb4b103900.png "redux store3")

컴포넌트의 스토어 구독<br />
G 컴포넌트는 스토어에 구독을 신청합니다<br />
구독을 하는 과정에서, 특정 함수가 스토어한테 전달이 됩니다<br />
스토어의 상태값에 변동이 생긴다면 전달 받았던 함수를 호출하게 됩니다.<br />
B 컴포넌트에서 어떤 이벤트가 생겨서, 상태를 변화 할 일이 생겼습니다. 이 때 dispatch 라는 함수를 통하여 액션을 스토어한테 던져줍니다.<br />
상태에 변화가 생기면, 이전에 컴포넌트가 스토어한테 구독 할 때 전달해줬었던 함수 listener 가 호출됩니다. 이를 통하여 컴포넌트는 새로운 상태를 받게되고, 이에 따라 컴포넌트는 *리렌더링*을 하게 됩니다.

![without redux](https://user-images.githubusercontent.com/35126809/64483923-f1a1cd00-d245-11e9-9b76-415b120857c6.png "without redux")

_With Redux vs Without Redux_

무조건 리덕스다 라는 것보다
적절한 상황에 적절하게 아키텍처를 설계 적용시키는게
바람직한 프로그래밍 방법이 아닐까 싶습니다.
