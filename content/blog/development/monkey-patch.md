---
title: What is Monkey patch?
date: 2020-11-08 14:11:66
category: development
thumbnail: { thumbnailSrc }
draft: false
---

# 참고

- [https://developer.mozilla.org/ko/docs/Web/JavaScript/New_in_JavaScript/ECMAScript_6_support_in_Mozilla](https://developer.mozilla.org/ko/docs/Web/JavaScript/New_in_JavaScript/ECMAScript_6_support_in_Mozilla)
- [https://developer.mozilla.org/ko/docs/Glossary/Polyfill](https://developer.mozilla.org/ko/docs/Glossary/Polyfill)

# 학습 목표

- 자주 보이는 몽키패치란 무엇인가 알아보기

## 몽키패치란?

프로그램이 런타임 되는 동안 사용되는 모듈이나 클래스를 변경하는 행동을 일컫는다.

원래는 'guerrilla patch' 라고 불리던게 고릴라 패치라고 잘못 불리게 되면서

고릴라보다 왜소한 원숭이로 대체해서 불리기 시작했다.

## 사용 시점

JavaScript 는 프로토타입 기반의 클래스 혹은 메소드를 작성할 수 있기 때문에 오염에 노출되어있다.

JavaScript 에서는 객체의 메소드를 선언시점이 아닌 사용 시점에 변경해서 사용할 수 있다.

```jsx
function mockFn() {
    arguments.forEach = Array.prototype.forEach;
    arguments.forEach(value => { console.log(value) });
}

mockFn(0,1,2);

//console.log() 메소드를 런타임에서 변경해서 아래와 같이 사용할 수 있다.
console.log_second = console.log
console.log = (arguments) => {console.log_second(`This is pollution ${arguments}`)}
console.log('console log') // This is pollution console log
```

## 장단점

일반적인 프로그래밍에선 몽키패치를 안티패턴이라고 말한다.

하지만 모던 자바스크립트에서 구형 브라우저들을 지원하기 위해 바벨같은 고마운 존재가 있다.

바벨은 트랜스파일러인데 [polyfill](https://developer.mozilla.org/ko/docs/Glossary/Polyfill) 이라는 기능도 포함하고 있다.

polyfill은 구형 브라우저들을 위해 모던 자바스크립트의 기능을 최대한 사용할 수 있게 몽키패칭해준다.

위와 같은 사례로는 좋은 의미의 몽키패치가 될 수 있고, 위 예시를 들은 사례로는 잘 동작하는 메소드를 mutable한 몽키패치라는 방식으로 오염을 시킬 수 있으므로 지양해야 한다.