---
title: TypeScript types
date: 2020-10-02 12:10:26
category: typescript
thumbnail: { thumbnailSrc }
draft: false
---

# 참고

- [oreilly](https://www.oreilly.com/library/view/programming-typescript/9781492037644/ch03.html)
- [TypeScript Handbook](https://typescript-kr.github.io/pages/basic-types.html)

# 학습 목표

- TypeScript에서 이용할 수 있는 타입을 살펴보고, 타입으로 무엇을 할 수 있는지 알아본다.

# 타입(type)이란?

값과 이 값으로 할 수 있는 일의 집합

타입은 타입검사기(typechecker)를 통해 유효하지 않은 동작이 실행되는 일을 예방한다.

타입 확인자는 특정 동작이 유효한지 아닌지 판단할 수 있다.

![TypeScript 타입 계층](https://user-images.githubusercontent.com/35126809/94884361-324f3280-04a8-11eb-87dd-975cd23f9d72.png)

> 출처: [https://www.oreilly.com/library/view/programming-typescript/9781492037644/ch03.html](https://www.oreilly.com/library/view/programming-typescript/9781492037644/ch03.html)
TypeScript 타입 계층

# 타입을 이야기하다

```jsx
function squareOf(n) {
	return n * n
}
squareOf(2) //4로 평가
squareOf('z') //NaN로 평가
```

`squareOf` 함수는 숫자를 파라미터로 받으며,  `*`키워드를 통해 곱셈의 숫자를 반환하는걸

코드상으로 예측 할 수 있다. 하지만 매개변수에 문자열 타입을 전달하면 의도와 다른 결과를 반환한다.

```tsx
function squareOf(n: number) {
	return n * n
}
squareOf(2) //4로 평가
squareOf('z') //에러 TS2345: 'z' 라는 타입의 인수는 'number' 타입의 매개변수에 할당할 수 없음
```

TypeScript에서는 숫자가 아닌 다른 타입을 인수로 전달하면 에러를 발생 시킨다.

- squareOf의 매개변수 n은 number로 제한된다.
- 2는 number에 할당 가능한 '타입'의 값이다.

TypeScript에서는 타입 어노테이션을 통해 타입의 제한을 둘 수 있고, 호출 및 할당을 제한 할 수 있다.

'TypeScript는 특정 타입이 와야 할때 이를 명시할 수 있는 언어' 라고 이해할 수 있다.'

# 타입의 가나다

타입의 종류와 타입 별칭, 유니온(합집합) 타입, 인터섹션(교집합) 타입 등을 알아본다.

## 불리언 Boolean

참(`true`) / 거짓(`false`)  값을 나타낸다.

```tsx
let isBool: boolean;
let isLoading: boolean = false;
```

## 숫자 Number

모든 부동소수점 값을 나타내고,

ECMAScript 2015에 소개된 2진수, 8진수 리터럴도 지원한다.

```tsx
let num: number;
let integer: number = 1;
let float: number = 3.14;
let hex: number = 0xf00d; // 61453
let binary: number = 0b1010; // 10
let octal: number = 0o744; // 484
let infinity: number = Infinity;
let nan: number = NaN;
let a: 26.218 = 26.218;
let b: a = 21 //에러 TS2322: '21'타입을 '26.218'에 할당할 수 없음
let oneMillion = 1_000_000 // 1000000 과 같음
let twoMillion: 2_000_000 = 2_000_000
```

# 문자열 String

문자열을 나타냅니다.

작은 따옴표 `'` , 큰따옴표 `"` 과 ES6 템플릿 문자열도 지원한다.

```tsx
let str: string;
let car: string = 'bmw';
let bicycle: string = 'citi100';
let brandCar: string = `${car} is brand car`;
let transport: string = 'My transport is' + bicycle;
let a: 'john' = 'john';
let b: 'john' = 'bob'; //에러 TS2322: 'bob' 타입을 'john' 타입에 할당 할 수 없음
```

## 배열 Array

배열 타입은 나타내는 타입 뒤에 `[]` 을 쓰거나, 제네릭 배열타입 `Array<elemType>`로 쓸 수 있다.

```tsx
let student: string[] = ['bob', 'john'];
//let student: Array<string> = ['bob', 'john'];
let unionTypeArr: (string | number)[] = ['a', 1 , 'b', 2, 3];

// interface type
interface Student {
	name: string,
	age: number
}

const students : Student[] = [
	{name:'john', age:20},
	{name:'bob', age:21}
]

// readonly type
let nums: readonly number[] = [1,2,3];
nums[0] = 0; // Error - TS2542: Index signature in type 'readonly number[]' only permits reading.
nums.push(4); // Error - TS2339: Property 'push' does not exist on type 'readonly number[]'.
```

## 튜플 Tuple

타입으로 고정된 길이를 가진 배열을 표현한다.

But, `push` , `splice` array prototype 메소드는 막을 수 없다..이 무슨?!

```tsx
let tuple: [boolean, number];
tuple = [true, 1];
tuple = [false, 1, 2]; // error
tuple = [1, false]; // error

let car: [number, string, boolean] = [300, 'bmw', true];
console.log(car[0]); // 300
console.log(car[1]); // 'bmw'
console.log(car[2]); // true

let car: [number, string, boolean][];
car = [[300, 'bmw', true], [100, 'hyundai', true], [200, 'kia', true]];

let student: [string, number];
student = ['bob', 20];
student.push('john');
console.log(student) // ['bob', 20, 'john']
student.push({name:'k', age: 21}); // Error - TS2345: Argument of type 'true' is not assignable to parameter of type 'string | number'
```

## 열거형 Enum

값을 할당하지 않으면 0 부터 시작한다.

역방향 매핑도 지원한다. (단, const enum 아닐때만 가능함.)

```tsx
enum Language {
	English,
	Spanish,
	Russian
}

console.log(Language[0]) // English
console.log(Language.English) // 0
console.log(Language.Spanish) // 1

enum Language {
	English = 10,
	Spanish = 200 + 300,
	Russian
}

console.log(Language[0]) // English
console.log(Language.English) // 10
console.log(Language.Russian) // 501

const enum Language {
	English = 10,
	Spanish = 200 + 300,
	Russian
}

console.log(Language[0]) // error const enum은 문자열 리터럴로만 접근할 수 있음
console.log(Language.English) // 10
console.log(Language.Russian) // 501
```

## 모든 타입 Any

어떤 타입의 값도 할당 할 수 있다.

최대한 지양하는걸 추천.

외부 라이브러리 사용시 @types 가 없는  경우 사용할 수 있다.

진짜 말그대로 다 할당 할 수 있어서 예시는 넘어간다.

## 알 수 없는 타입 Unknown

최상위 타입인 `unknown` 은 어떤 타입의 값도 할당 할 수 있지만,

`unknown` 을 다른 타입에는 할당 할 수 없다.

```tsx
let a: any = 'abc';
let u: unknown = 123;

let can: boolean = a; // 성공 any는 만능.
let cant: number = u; // 실패 unknown은 any를 제외한 다른 타입에 할당할 수 없다.
let can2: any = u; // 성공
let can3: number = u as number; // 타입단언시에 가능

let a: unknown = 55
let b = a === 123 // false => b는 boolean 타입이 됨.
```

## 객체 Object

`typeof` 연산자가 `object` 로 반환하는 타입.

tsconfig 에서 `string : true` 이면 `null` 은 object가 아님 (null 아웃)

사실 거의 모든 상위 타입이라 비추천.

객체라면 속성들에 대해 인터페이스나 타입 생성을 추천.

```tsx
//비추
let obj: object = {};
let arr: object = [];
let func: object = function () {};
let date: object = new Date();

//추천
interface IStudent {
	name: string,
	age: number
}

let student: { name: string, age: number } = {
  name: 'bob',
  age: 20
};

let student: IStudent = {
  name: 'bob',
  age: 20
};
```

## 큰 정수 Bingint

`number`는 253까지 정수를 표현하지만, `bigint` 는 더 큰 수도 표현 가능.

BigInt는 정수 리터럴의 뒤에 n을 붙이거나(10n) 함수 BigInt()를 호출해 생성할 수 있다.

```tsx
let a = 2134n //bigint
const b = 5785n //5785n
let e = 77.5n //에러 정수여야됌
let f: bigint = 100n //bigint
```

## null, undefined

`null`, `undefined` 는 모든 타입의 하위 타입이다.

```tsx
let a = string = null;
let b = string = undefined;
let obj = {a:1} = undefined;
let obj = {a:1} = null;
let v = void = null;
let v = void = undefined;
```

## void

반환 할 값이 없는 함수에서 사용한다.

```tsx
function helloworld(name: string): void {
	console.log(`hello world ${name}`);
}
const hi: void = helloworld('bob');
console.log(hi); // undefined
```

## never

절대 발생할 수 없는 타입.

절대 반환하지 않는 타입.

타입 가드에 의해 아무 타입도 얻지 못하게 좁혀지면 `never` 타입을 얻게됨.

`unknown` 과 다르게 `any` 조차도 할당 할 수 없는 타입. 오직 본인만 본인에게 할당 가능.

```tsx
function errorFunc() {
    return error("Something wrong!"); 
}

function errorFunc(message: string): never {
    throw new Error(message);
}
```

# 함수 function

화살표 함수 통해서 타입 지정이 가능하다.

인수와 반환 값의 타입을 지정할 수 있다.

```tsx
let func: (arg1: number, arg2: number) => number;
func = function (x:number, y:number): number {
  return x + y;
};

let func: () => void;
func = function () {
  console.log('Hi bob');
};
```

# 타입 별칭

별칭이란 변수를 선언해서 값 대신 변수로 칭하듯이 '타입 별칭' 으로 타입을 가리킬 수 있다.

TypeScript는 별칭을 추론하지 않으므로 반드시 별칭의 타입을 명시적으로 정의해야 한다.

```tsx
type Age = number
type Person = {
	name: string
	age: Age
}

let age: Age = 20
let driver: Person = {
	name:'bob',
	age: age
}

// Age는 number의 별칭이므로 number에도 할당할 수 있다.
let age = 20
let driver: Person = {
	name:'bob',
	age: age
}
```

## 유니온(union)

`Or 조건`이라고 생각하면 된다.

2개 이상의 타입을 허용하는 경우, `|` 키워드를 통해 구분하고, `()` 는 선택사항.

```tsx
let union: string | number;
let unionOptional: (string | number);
union = 'Union type';
union = 123;
union = false; // Error - TS2322: Type 'false' is not assignable to type 'string | number'.
```

## 인터섹션(Intersection)

유니온과 반대. `And 조건`이라고 생각하면 된다.

2개 이상의 타입을 조합하는 경우, `&` 키워드를 통해 사용 가능하다.

```tsx
interface IStudent {
  name: string,
  age: number
}

interface IOff {
  isOff: boolean
}

const who: IStudent & IOff = {
  name: 'bob',
  age: 20,
  isOff: true
};
```

# 선택형 프로퍼티 및 인덱스 시그니처

수많은 속성을 가지거나 단언할 수 없는 임의의 속성이 포함되는 구조에서 사용가능한 인덱스 시그니처.

인덱싱에 사용할 인덱서(Indexer)의 이름과 타입 그리고 인덱싱 결과의 반환 값 타입을을 지정한다.

**인덱서의 타입은 `string`과 `number`만 지정할 수 있다.**

```tsx
let a : {
	b: number,
	c?: string,
	[key:number]: boolean | string | number[]
}

interface IAlphabet {
  [alphabetIndex: number]: string
}
let alphatbet: IAlphabet = ['a', 'b', 'c']; // Indexable type
console.log(alphatbet[0]); // 'a' is string.
console.log(alphatbet[1]); // 'b' is string.
console.log(alphatbet['0']); // Error - TS7015: Element implicitly has an 'any' type because index expression is not of type 'number'.
```
