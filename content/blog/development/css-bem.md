---
title: "CSS BEM 디버깅 절약을 위한 규칙"
date: 2018-10-11 21:00:00
category: development
thumbnail: { thumbnailSrc }
draft: false
---

웹은 정말 빠른 속도로 발전하고 있습니다.  
CSS는 훌륭한 **_'언어'_**라고는 할수 없지만 빠르게 진화했습니다.

대규모 어플리케이션이 많아지고 그에따라 CSS 활용 범위도 넓어져가는데,  
(잘못 작성된 CSS는 디버깅하는데 헬파티를 열어준다.)  
설계에 의미를 두지 않았던 CSS에도 다양한 방법론 생겨났습니다.

CSS방법론(BEM, SMACSS, OOCSS 등 더 많을수도..?) 존재하지만
이번 포스팅에서는 BEM에 관해서 적어볼까 합니다.

CSS 방법론은 아래와 같은 목표를 갖고 있습니다.

- 쉬운 유지보수
- 코드의 재사용성
- 확장성
- 클래스만으로 의미 추론

---

## CSS 명명규칙을 통해서 무엇을 해결하고자 하는가?

- selector의 이름만 보고 role을 알 수 있다.
- selector를 보기만 해도 어디에 사용할 수 있는지 알 수 있다.
- 클래스간의 관계는 클래스명만으로도 유추가 가능하다.

## BEM (Block, Element, Modifier) 정의

- [BEM](http://getbem.com/)은 Block Element Modifier의 약자
- [OOP(Object Oriented Programming)](https://developer.mozilla.org/ko/docs/Learn/JavaScript/Objects/Object-oriented_JS)와 유사한 형태를 띈다.
- 반드시 class명만 사용할 수 있다

### Block

> 곰돌이푸 베어브릭 가지고 쉽게 설명해보겠습니다.

- **Block은 구성 요소(component = Block)를 나타냅니다.** (element를 포함하고있는 컨테이너 요소)
- ex) content, navigation, header, footer

![곰돌이푸](https://user-images.githubusercontent.com/35126809/52403067-311a0680-2b09-11e9-9ac5-eb0ae401ad98.jpg "곰돌이푸베어브릭")

> 아래와 같이 component 스타일이 명명될 수 있습니다.

```css
.pooh {
}

//or

.pooh-brick {
}
```

### Elements

- 'BEM'의 E는 요소(Elements)를 가르킵니다.
- element는 block 안에서 기능을 가진 컴포넌트입니다.
- 곰돌이푸의 '머리,가슴,팔,다리' 는 component 내의 elements 요소입니다.
- **전체 상위 구성 요소(parent component)의 하위 구성 요소(child components)**로 판단 할 수 있습니다.
- element 이름이 길 경우 – 하이픈으로 연결해도 된다.(그건 본인마음 or 코딩 컨벤션)

> element 는 이름 뒤에 2개의 밑줄(two underscores)을 추가하여 클래스를 지정할 수 있습니다.

```css
.pooh__head {
}
.pooh__body {
}
.pooh__arms {
}
.pooh__feet {
}

//or

.pooh-brick__head {
}
.pooh-brick__body {
}
.pooh-brick__arms {
}
.pooh-brick__feet {
}
```

### Modifiers

- 'BEM'의 M은 수정(Modifiers) 이다.
- 곰돌이푸 자체가 노란색 몸을 가졌지만, 파란색, 빨간색이 될 수도 있다.
- Modifier는 block 또는 element의 속성이다.
- block 또는 element의 스타일 혹은 상태를 변화시킨다.

> block 또는 element에 하이픈(hyphens) 두 개를 추가하여 지정한다.

```css
.pooh--yellow {
  background: #yellow;
}
.pooh--red {
  background: #red;
}
.pooh--blue {
  background: #blue;
}

//or
.pooh-brick--yellow {
  background: #yellow;
}

.pooh-brick--red {
  background: #red;
}

.pooh-brick--blue {
  background: #blue;
}
```

> 만약 머리색만 바뀐 곰돌이푸라면 ??

```css
.pooh__head--red {
  background: #red;
}

//or

.pooh-brick__head--red {
  background: #red;
}
```

위가 베이직한 BEM 명명 규칙이 작동하는 방식입니다.
단순한 프로젝트의경우는 하이픈으로만 클래스를 지정하게되는데 대규모 어플리케이션은
유지보수 및 확장성을 위해 BEM 규칙을 사용하길 권장한다.  
_물론 깊이가 깊어지면 또다른 혼란을 가져올 수 있다..._
