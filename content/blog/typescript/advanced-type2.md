---
title: TypeScript advanced type second
date: 2020-10-25 12:10:87
category: typescript
thumbnail: { thumbnailSrc }
draft: false
---

## 참고

- [O'REILLY](https://www.oreilly.com/library/view/programming-typescript/9781492037644/)
- [TypeScript Handbook](https://typescript-kr.github.io/pages/basic-types.html)
- [TypeScript Playground](https://www.typescriptlang.org/play)

## 학습 목표

- TypeScript 제어 흐름 기반 타입 확인, 타입 어서션, 프로토타입 확장하기 등의 패턴 알아보기

## 타입 어서션

TypeScript의 컴파일러가 타입을 실제 런타임에 존재할 변수와 다르게 추론하거나 타입에 대해 보수적일때

수동으로 타입을 구체화 할때 사용한다.

```tsx
class User {
  name: string
}

class One extends User {
	introudce() {
		// ...
	}
}

(<Onea>User).introduce(); //type assertions
(User as One).introduce(); //type assertions
```

## 튜플 타입 추론 개선

타입 어서션을 사용하지 않고 추론 범위도 좁히지 않으면서 튜플을 튜플 타입으로 만들려면 어떻게 해야 할까?

```tsx
function tuple<T extends unknown[]>(..ts: T): T{
	return ts
}

let a = tuple(1, true) // [number, boolean]
```

## 타입 가드

타입 가드는 런타임에서의 타입체크를 컴파일러에게 알려주는 기능이다.

타입가드는 TypeScript의 내장 기능으로 `typeof` 와 `instanceof` 로 타입을 정제할 수 있게 해준다.

자신만의 타입가드가 필요한데 이때는 `is` 연사자를 사용한다. 

```tsx
function isString(a: unknown): boolean {
  return typeof a === 'string';
}
isString('a') // true
isString(true) // false

function parseInput(input: string | number) {
  let formattedInput: string;
  if (isString(input)) {
    // input: string | number
    formattedInput = input.toUpperCase();
    /**
     * Property 'toUpperCase' does not exist on type 'string | number'.
      Property 'toUpperCase' does not exist on type 'number'.ts(2339)
    */
  }
}
```

위 코드에서는 타입스크립트가 알고 있는 사실은 `isString`이 `boolean`을 반환한다는 것이다.

`isString`이 `boolean` 반환 뿐 아니라 `boolean` 이 `true` 일시 인수가 `string`임을 알려줘야 한다.

타입가드를 이용해 해결할 수 있다.\

```tsx
function isString(a: unknown): a is string {
	return typeof a === 'string'
}

//유니온과 인터섹션 같은 복합 타입에도 적용이 가능하다.
type Black = // ...
type White = // ...

function isBlack(color: Black | White): color is Black{
	// ...
}
```

## 조건부 타입

U와 V 타입에 의존하는 T 타입을 선언하라. U <: V면 T를 A에 할당하고, 그렇지 않으면 T를 B에 할당하라 라는 말을 예시로 들 수 있는 바로 코드를 보자

```tsx
type IsString<T> = T extends string ? true: false
type A = IsString<string> //true
type B = IsString<number> //false
```

## 분배적 조건부

이 표현식 `(string | number) extends T ? A : B` 은 `(string extends T ? A : B) | (number extends T ? A : B)` 다음과 같다.

```tsx
type ToArray<T> = T[];
type A = ToArray<string>; // stirng[]
type B = ToArray<number | string>; // (number | string)[]

type ToArray2<T> = T extends unknown ? T[] : T[];
type C = ToArray<string>; // stirng[]
type D = ToArray<number | string>; // number[] | string[]

type E = (number extends unknown ? number[] : never) // number[] | string[]
            | (string extends unknown ? string[] : never)

type F = never
			| number
			| string

type G = number | string
```

## infer 키워드

제네릭 타입을 인라인으로 선언할 수 있는 전용 문법을 infer 키워드를 통해 제공한다.

```tsx
//배열의 요소 타입을 얻는 ElementType 이라는 조건부 타입을 정의한다.
type ElementType<T> = T extends unknown[] ? T[number] : T;
type A = ElementType<number[]>; // number

//infer문으로 새로운 타입 변수 U를 선언했다
//ElementType2에 어떤 T를 전달했느냐를 보고 U의 타입을 추론한다. 
type ElementType2<T> = T extends (infer U)[] ? U : T;
type B = ElementType2<number[]>; // number
```

## 내장 조건부 타입들

```tsx
// Exclude<T, U> : T에 속하지만 U에 없는 타입을 구함
type A = number | string;
type B = string;
type C = Exclude<A, B>; // number

// Extract<T, U> : T의 타입 중 U에 할당할 수 있는 타입을 구함
type D = Extract<A, B>; // string

// NonNullable<T> : null 과 undefined 를 제외한 타입을 구함
type X = null | undefined;
type E = NonNullable<A | X>; // string | number
type Y = {
  a?: string | null;
};
type F = NonNullable<A | X>; // string | number
type G = NonNullable<Y['a']>; // string

// ReturnType<F> : 제네릭, 오버로드된 함수 제외하고 함수의 반환 타입을 구한다.
type H = (a: string) => A;
type I = ReturnType<H>; // string | number // (=== A타입)

// InstanceType<C> : 클래스 생성자의 인스턴스 타입을 구한다.
type K = { new(): K };
type J = { b: string };
type L = InstanceType<J>; // { b: string }
```

## 탈출구

### 타입 어서션

as문법 사용을 권장한다. react 에서 TSX <> 문법과 혼동을 일으키기 때문이다.

```tsx
function formatInput(input: string) {
  // ...
}

function getUserInput(): string | number {
	// ...
}

const input = getUserInput(); // string | number

//둘다 같은 의미
formatInput(input as string); 
formatInput(<string>input);
```

### Nonnull 어서션

`T | null` 또는 `T | null | undefined` 타입을 대비해 타입스크립트는 어떤 값의 타입이 null 이나

undefined가 아니라 T 임을 단언하는 특수 문법을 제공한다.

```tsx
type Dialog = {
  id?: string;
};

function closeDialog(dialog: Dialog) {
  if (!dialog.id) return;

  setTimeout(() => {
    removeFromDom(dialog, document.getElementById(dialog.id!)!);
  });
}

function removeFromDom(dialog: Dialog, element: Element) {
  element.parentNode!.removeChild(element);
  delete dialog.id;
}
```

### 확실한 할당 어서션

타입스크립트가 변수를 사용할 때 값이 이미 할당되어 있는지 검사하는 방법이다.

너무 자주 사용하지 말자.

```tsx
let userId!: string;
fetchUser();

userId.toUpperCase();

function fetchUser() {
  userId = 'one';
}
```

### 이름 기반 타입 흉내내기

타입스크립트는 구조 기반 타입에 기반하지만 이름 기반 타입을 흉내낼수 있는 방법이 있다.

```tsx
type CompanyID = string;
type CartID = string;
type OrderID = string;

function forCart(id: CartID){
  //...
}

const OrderID: OrderID = '20201bcdfdBdwET';
forCart(OrderID); // 구조 기반 타입이라 cart함수에 OrderID를 넣어도 오류가 안남.
```

브랜디드 타입을 이용하면 프로그램을 더 안전하게 만들 수 있다.

`unique symbol`을 '브랜드'로 사용하고 이 브랜드를 `string`과 인터색션하여 브랜디드 타입과 같다고 어서션할 수 있다.

```tsx
type CompanyID = string & { readonly brand: unique symbol };
type CartID = string & { readonly brand: unique symbol };
type OrderID = string & { readonly brand: unique symbol };

function CompanyID(id: string) {
  return id as CompanyID;
}
function CartID(id: string) {
  return id as CartID;
}
function OrderID(id: string) {
  return id as OrderID;
}

const companyID = CompanyID('1g172adf'); // CompanyID
const cartID = CartID('9a8adb'); // CartID
const orderID = OrderID('0a8fdz'); // OrderID

forCart(cartID) //ok
forCart(orderID) // TS Error!
// Argument of type 'orderID' is not assignable to parameter of type 'CartID'.
```

### 프로토타입 안전하게 확장하기

```tsx
function tuple<T extends unknown[]>(..ts: T): T{
	return ts
}

// 타입스크립트에 zip이 무엇인지 설명
interface Array<T> {
  zip<U>(list: U[]): [T, U][]
}

// .zip 구현
Array.prototype.zip = function <T, U>(
  this: T[],
  list: U[]
): [T, U][] {
  return this.map((val, idx) => tuple(val, list[idx]));
}

//사용한 결과
import './zip';
[1, 2, 3].map(n => n * 2).zip(['a', 'b', 'c']);
// [[2, 'a'], [4, 'b'], [6, 'c']]
```