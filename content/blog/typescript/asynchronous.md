---
title: TypeScript 비동기 프로그래밍 동시성과 병렬성
date: 2020-11-07 10:11:31
category: typescript
thumbnail: { thumbnailSrc }
draft: false
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

## 비동기 스트림

서로 다른 시점에 이용할 수 있게 되는 값.

각각의 데이터를 미래의 어떠한 시점에 받게 된다는 점을 아래와 같은 방법으로 설계할 수 있다.

1. `NodeJS` Event Emitter (이벤트 방출)
2. `RxJS` 리액티브 프로그래밍 라이브러리

## 이벤트 방출

채널로 이벤트를 방출하고 채널에서 발생하는 이벤트를 리스닝 하는 API를 제공한다.

```tsx
interface Emitter {
	//이벤트 방출
	emit(channel: string,  value: unknown): void
  //이벤트 방출되었을 때 작업 수행
	on(channel: string, f: (value: unkown) => void): void
}
```

`NodeRedis` 를 이용한 Event Emitter 예제

```tsx
import Redis from 'redis'

let client = redis.reateClient()

client.on('ready', () => console.info('Client is ready'))
client.on('error', e => console.error('An error occurred!', e))
client.on('reconnecting', params => console.info('Reconnecting...', params))

//오버로드된 타입 사용
type RedisClient = {
	on(event: 'ready', f: () => void): void
	on(event: 'error', f: (e: Error) => void): void
	on(event: 'reconnecting', f (params: {attempt: number, delay: number}) => void): void
}

//더 간결하고 안전한 매핑 타입 정의
type Events = {
	ready: void
	error: Error
	reconnecting: { attempt: number, delay: number }
}

type RedisClient = {
	on<E extends keyof Events>(
		event: E,
		f: (arg: Events[E]) => void
	): void
	emit<E extends keyof Events>(
		event: E,
		arg: Events[E]
	): void
}
```

이벤트 이름과 인수를 하나의 형태로 따로 빼고, 리스너와 방출을 생성해서 매핑하는 패턴.

## 타입 안전 멀티스레딩

작업을 여러 개의 스레드로 분리하고 속도를 높이거나 부하를 줄여 반응성을 높이는 일.

브라우저와 서버에서 안전하게 병렬 프로그래밍을 할 수 있는 패턴

### 브라우저에서 웹 워커 활용

웹 워커는 코드를 다른 CPU 스레드에서 병렬로 실행하도록 한다.

웹 워커는 브라우저에서 제공하는  API 이므로 설계자들은 안전성에 최우선 한다.

웹 워커가 메인 스레드나 다른 웹 워커와 통신하는 주된 수단은 메세지 전달이 되어야 한다.

```tsx
//MainThread.ts
let worker = new Worker('WorkerScript.ts')
worker.onmessage = e => {
	console.log(e.data) // 'Ack: new name' 기록
}
worker.postMessage('new name')

//WorkerScript.ts
onmessage = e => {
	console.log(e.data) // 'new name' 기록
	postMessage(`Ack: "${e.data}"`)
}
```