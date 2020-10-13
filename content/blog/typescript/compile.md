---
title: TypsScript 동작 원리, 기능, 패턴
date: 2020-09-30 16:09:63
category: typescript
thumbnail: { thumbnailSrc }
draft: false
---

# TypeScript

Static type

compile time

# JavaScript

Dynamic type

run time

---

JavaScript는 동적 타입 언어이며, 런타임에 변수 타입이 결정된다.

TypeScript는 JavaScript의 모든 기능을 포함하고, 동적 타입 언어이다.

동적 타입 언어는 컴파일 타임에 변수 타입이 결정된다.

- 동적 타입 언어 e.g.
    - Python
    - PHP
- 정적 타입 언어 e.g.
    - Java
    - C++

# 비교

# 정적 타입 언어

진입 장벽이 높다.

코드 양이 많으면 생산성이 높다.

타입 오류가 컴파일 시 발견 된다.

# 동적 타입 언어

진입 장벽이 낮다.

코드 양이 적을 때 생산성이 높다.

타입 오류가 런타임 시 발견된다.

---

# TypeScript compile

TypeScript 컴파일을 보기전에 [야크 털 깎기](https://www.notion.so/juunone/Yak-Shaving-628846ea37a349ac82cf1a9a73fabed6)를 하게 되는데...JavaScript 컴파일[(JITC)](https://juunone.netlify.app/javascript/jitc/) 부터 확인 해보자.

TypeScript에서의 컴파일 과정은  

TypeScript text parse —> Abstract syntax tree, AST(추상 구문 트리)로 변환 —> JavaScript 코드로 변환

**단, TypeScript compiler는 AST를 만들기 전 Type checker(타입 확인)을 거친다.**

위 과정 이후는 변환된 JavaScript 텍스트 소스 컴파일 과정을 진행한다.

# 타입은 어떻게 결정되는가?

TypeScript는 **점진적으로 타입을 확인**하는(gradually typed) 언어이다.

Java와 같은 정적 언어와 **다르게** 컴파일 타임에 모든 타입을 알고 잇지 않아도 컴파일이 가능해 결과를 보여줄 수 있다.

TypeScript는 타입 추론 이라는 강력한 기능을 통해 타입을 추론해서 오류를 검출 할 수 있다.

점진적 타입 확인은 JavaScript를 TypeScript로 migration 할때 유용하다.

하지만 그 이외의 상황에서는 모든 코드에 타입이 컴파일 타임에 식별되도록 하는 방식을 추구 하는게 좋다.

# 언제 타입을 검사하는가?

![tsnode](https://user-images.githubusercontent.com/35126809/94658307-97d2e000-033d-11eb-91c7-b5c4f469eea9.png)

`ts-node`를 이용해서 예시를 만들어 봤다.

JavaScript라면 '13' 이라는 문자열이 나오겠지만 TypeScript는 오류를 검출 해준다.

정적으로 코드를 분석하고 코드가 컴파일에 실패한 이유를 친절하게 에러 메세지와 같이 알려 준다.

**TypeScript는 컴파일 타임에 문법 에러와 타입 관련 에러 모두를 검출한다.**