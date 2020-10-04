---
title: TypeScript 함수 선언과 호출
date: 2020-10-04 22:10:69
category: typescript
thumbnail: { thumbnailSrc }
draft: false
---

# 참고

- [O'REILLY](https://www.oreilly.com/library/view/programming-typescript/9781492037644/)
- [TypeScript Handbook](https://typescript-kr.github.io/pages/basic-types.html)

# 학습 목표

- TypeScript에서 함수를 선언하고 실행하는 다양한 방법
- 시그니처 오버로딩
- 다형적 함수
- 다형적 타입 별칭

# 함수 선언과 호출

TypeScript 함수 작성시 반환 타입을 추론 하도록 하는 걸 권장한다.

JavaScript 함수는 일급(first-class) 객체이다.

JavaScript TypeScript는 최소 다섯 가지 함수 선언 방법을 지원한다.

```tsx
//함수 선언식
function world(name: string) {
	return `hello ${name}`;
}

//함수 표현식
let world2 = function(name: string) {
	return `hello ${name}`;
}

//화살표 함수 표현식
let world3 = (name: string) => {
	return `hello ${name}`;
}

//단축형 화살표 함수 표현식
let world4 = (name: string) => return `hello ${name}`;

//함수 생성자는 되도록 사용하지 않는 편이 좋다. 타입이 Functino이기 때문에 모든 프로토타입 메소드를 포함한다.
let world5 = new Function('name', 'return "hello " + name');
```

TypeScript에서는 함수 호출 시 따로 타입 정보를 제공할 필요 없으며,

인수를 전달하면 함수의 매개 변수와 인수의 타입이 호환 되는지 확인한다.

당연히 잘못된 타입의 인수를 전달하면 에러를 뱉는다.

## 선택적 매개변수

객체와 튜플 타입 에서 처럼 함수 에서 도 ' `?` ' 키워드를 이용해 함수의 매개 변수를 선택적으로 지정할 수 있다.

필수 매개 변수는 앞쪽, 선택적 매개 변수는 뒤에 위치하도록 추가한다.

```tsx
function log(message: string, userId?: string) {
	console.log(message , userId || 'Not signed in')
}

log('Page loaded') // Not signed
log('User signed in', 'be451e') //User signed in be451e

//기본 매개변수에도 타입을 명시할 수 있다.
type Context = {
	appI?: string
	userId?: string
}

function log(message: string, context: Context = {}) {
	console.log(message, context.userId);
}
```

## 나머지 매개변수(rest parameters)

안전한 타입의 가변 인수 함수를 만들 때 사용하는 방법이다.

```tsx
function sumVariadicSafe(...numbers: number[]): number {
	return numbers.reduce((acc,cur)=> acc + cur, 0);
}

sumVariadicSafe(1, 2, 3) //6

//TypeScript 내장 console.log 생김새
interface Console {
	log(message?: any, ...optionalParams: any[]): void
}
```

## this의 타입

메소드 호출 시 this는 '점' 왼쪽의 값을 갖는다는 것이 일반적인 원칙이다.

TypeScript의 함수에서 `this` 사용할 때는 항상 this 타입을 함수의 첫번째 매개 변수로 선언하자.

함수 안에서 사용하는 모든 `this` 가 의도한 this 타입으로 보장해준다.

```tsx
function fancyDate(this: Date){
	return `${this.getDate()} / ${this.getMonth()} / ${this.getFullYear()}`;
}
```

## 호출 시그니처 혹은 타입 시그니처

함수에 함수를 인수로 전달하거나 함수에서 다른 함수를 반환하는 경우 이 문법으로 인수나 반환 함수의 타입을 지정할 수 있다.

함수 호출 시그니처는 오직 '타입 정보'만 포함한다.

```tsx
//단축형 호출 시그니처
type Log = (message: string, userId?: string) => void

//매개변수 타입, 반환 타입이이 이미 호출 시그니처에 있어 따로 정의할 필요가 없다.
//여기서 message의 타입을 Log 타입을 통해 string 으로 
//추론하는걸 문맥적 타입화(contextual typing)라는 TypeScript 추론 기능이다.
let log: Log = (message, userId = 'Not signed in') => {
	console.log(message, userId);
}
```

## 오버로드된 함수 타입

호출 시그니처가 여러 개인 함수.

```tsx
type CreateElement = {
	(tag: 'a'): HTMLAnchorElement
	(tag: 'canvas'): HTMLCanvasElement
	(tag: 'table'): HTMLTableElement
	(tag: string): HTMLElement
}

let createElement: CreateElement = (tag: string): HTMLElement => {}
```

선언한 순서대로 오버로드를 해석하므로(리터럴 오버로드를 일반 오버로드 보다 먼저 처리한다)

지정되지 않은 문자열을 createElement로 전달하면 HTMLElement  로 분류한다.

CreateElement는 'a' | 'canavas' | 'table' | string 3개의 타입은 string 의 서브타입으로 유니온 된 결과를 

`string` 으로 축약할 수 있다.

# 다형성

어떤 타입을 사용할지 미리 알 수 없는 상황이 발생할 수 있는데, 특정 타입을 제한하기 어렵다.

## **제네릭 타입 매개변수**

- 여러 장소에 타입 수준의 제한을 적용 할때 사용하는 플레이스 홀더 타입.
- 다형성 타입 매개 변수 라고도 부른다.

```tsx
type Filter = {
	<T>(array: T[], f: (item: T) => boolean): T[]
}

let filter: Filter = (array,f) => {}

filter([1,2,3], _) => _ > 2 //T = number
filter(['a','b','c'], _) => _ !== b //T = string
```

전달된 array의 타입을 보고 T의 타입을 추론한다.

filter를 호출한 시점에 T의 타입을 추론하면 filter에 정의된 모든 타입은 T를 추론한 타입으로 대체된다.

T는 자리를 맡아 둔다는 의미의 '플레이스 홀더' 타입이며, 타입 검사기가 문맥을 보고 플레이스 홀더 타입을 

실제 타입으로 채운다. `T` 는 filter의 타입을 매개 변수화 한다.

그래서  `T` 는 제네릭 타입 매개 변수라고 부른다.

꺾쇠괄호(<>) 로 제네릭 타입을 선언한다. 필요시 여러개를 콤마로 구분해 선언할 수 있다.

## 제네릭 타입 한정 시점

```tsx
//호출 시그니처의 일부에 선언 실제 호출할때 구체 타입이 T이다.
type Filter = {
	<T>(array: T[], f: (item: T) => boolean): T[]
}

//Filter 타입 별칭에 한정하므로 타입을 명시적으로 한정해야 한다.
type Filter<T> = {
	(array: T[], f: (item: T) => boolean): T[]
}

let filter: Filter<number> = (array, f) => {}
```

## 상한 경계 제네릭 타입

```tsx
type HasPoint = { numberOfPoint: number }
type PointLength = { pointLength: number }
type Square = HasPoint & PointLength
let square: Square = {numberOfPoint: 4 , pointLength: 2}

function getSquare<Shape extends HasPoint && PointLength> (s: Shape): Shape{
	console.log(s.numberOfPoint * s.pointLength);
	return s
}

getSquare(square); //8

```

## 제네릭 타입 기본값

```tsx
type Event<T extends HTMLElement = HTMLElement> = {
	target: T
	type: string
}

let event: Event = {
	target: element,
	type: string
}
```

### 타입 주도 개발(type-driven development): 타입 시그니처를 먼저 정하고 값을 나중에 채우는 프로그래밍 방식