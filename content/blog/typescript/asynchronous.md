---
title: TypeScript 비동기 프로그래밍 동시성과 병렬성
date: 2020-11-07 10:11:31
category: typescript
thumbnail: { thumbnailSrc }
draft: true
---

## 참고

- [O'REILLY](https://www.oreilly.com/library/view/programming-typescript/9781492037644/)
- [TypeScript Handbook](https://typescript-kr.github.io/pages/basic-types.html)

## 학습 목표

- 비동기라는 기능이 자바스크립트 엔진에서 어떻게 동작하는지와 단일 스레드에서 어떻게 실행을 정지하고 재개할 수 있는지 살펴본다.

## TypeScript 에서 Promise 사용법

`new Promise`는 실행자 라고 부르는 함수를 인수로 받으며, `Promise` 구현에서 `resolve` 함수와 `reject` 함수를 인수로 건네 함수를 호출한다.

```tsx
//Promise의 일부 기능 직접 구현해보기
type Executor = (
    resolve:Function,
    reject:Function
) => void
class Promise {
    constructor(f: Executor) {}
}

//Function 타입 구체화하기
type Executor<T, E extends Error> = (
	resolve: (result: T) => void,
	reject: (error: E) => void
) => void

class Promise<T, E extends Error> {
	constructor(f: Executor<T, E>) {}
}

//Promise 생성자 API에 then, catch 연산 추가하기
class Promise<T, E extends Error> {
	constructor(f: Executor<T, E>) {}
	then<U, F extends Error>(g: (result: T) => Promise<U, F>): Promise<U, F>
	catch<U, F extends Error>(g: (error: E) => Promise<U, F>): Promise<U, F>
}

let a: () => Promise<string, TypeError>
let b: (s: string) => Promise<number, never>
let c: () => Promise<boolean, RangeError>

a()
	.then(b)
	.catch(e => c())
	.then(result => console.info('Done', result))
	.catch(e => console.error('Error', e))

// a => 에러 => catch c 에러 => console.error('Error', e) 호출
// a => 에러 => catch c 성공 => console.info('Done', result) 호출
// a => 성공 => then b 호출 / b 는 never를 두번째 인수가 never 이므로 에러를 던지지 않는다.

//타입스크립트는 자바스크립트의 동작을 상속받아 Error에 에러뿐 아니라
//문자,함수,배열등이 포함될 수 있기 때문에 에러를 느슨하게 풀어준다.
type Executor<T> = (
	resolve: (result: T) => void,
	reject: (error: unknown) => void
) => void

class Promise<T> {
	constructor(f: Executor<T>) {}
	then<U>(g: (result: T) => Promise<U>): Promise<U>
	catch<U>(g: (error: unknown) => Promise<U>): Promise<U>
}
```