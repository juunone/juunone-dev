---
title: TypeScript Error handling
date: 2020-10-29 11:10:00
category: typescript
thumbnail: { thumbnailSrc }
draft: false
---

## 참고

- [O'REILLY](https://www.oreilly.com/library/view/programming-typescript/9781492037644/)
- [TypeScript Handbook](https://typescript-kr.github.io/pages/basic-types.html)

## 학습 목표

- TypeScript에서 에러를 표현하고 처리하는 가장 일반적인 패턴을 학습한다.

## 에러를 표현하는 패턴

각각의 에러를 표현하는 방식은 장단점이 존재하면 개발자의 의도와 상황에 따라 장단점을 가질 수 있다.

- `null` 반환
- 예외 던지기
- 예외 반환
- Option 타입

## null 반환

타입 안정성을 유지하면서 에러를 처리하는 가장 간단한 방법은 `null` 을 반환하는 것이다.

하지만 `null`을 반환하면 모든 연산에서 `null`에 대한 예외 처리를 추가해야 하므로 코드가 길어진다.

`—strictNullChecks = true`  를 사용하게 되면 `null` 과 `undefined`는 `any` 타입과 스스로의 타입 외엔

할당할 수 없으므로 `null` 반환 에러 처리를 사용할 수 없다.

```tsx
/** 
* 사용자의 생일을 받아 객체로 파싱하는 함수
* 입력된 내용을 파싱하는 단계에서 결과가 null인지 확인한다.
*/

function ask() {
    return prompt('When is your birthday?')
}

function isValid(date: Date){
    return Object.prototype.toString.call(date) === '[Object Date]' &&  !Number.isNaN(date.getTime())
}

function parse(birthday: string): Date | null {
    let date = new Date(birthday)

    if(!isValid(date)) {
        return null
    }
    return date
}

let date = parse(ask())

if(date){
    console.info('date', date.toISOString())
}else{
    console.error('date')
}
```

## 예외 던지기

문제가 발생하면 null 반환 대신 예외를 던지면 디버깅을 할때 좀더 직관적인 메타 데이터를 얻을 수 있다.

```tsx
/**
* 에러를 서브클래싱하여 더 구체적으로 표현하여 범용성 있게 사용할 수 있다.
* 서브클래싱한 에러를 다른 개발자가 추가한 에러와 구분도 가능하다.
* 함수 이름에 명시하거나 문서화 주석에 정보를 추가함으로서 에러에 대한 개런티 가능하다.
*/

/**
* @throws {InvalidDateFormatError} 사용자가 생일을 잘못 입력함
* @throws {DateIsInTheFutureError} 사용자가 생일을 미래 날짜로 입력함
*/
class InvalidDateFormatError extends RangeError {}
class DateIsInTheFutureError extends RangeError {}

function parse(birthday: string): Date | null {
    let date = new Date(birthday)

    if(!isValid(date)){
        throw new InvalidDateFormatError('Enter a date in the form YYYY/MM/DD')
    }

    if(date.getTime() > Date.now()) {
        throw new DateIsInTheFutureError('Are you in time machine?')
    }
    return date
}

try {
    let date = parse(ask())
    console.info('Date is', date.toISOString())
}catch (e) {
    if(e instanceof InvalidDateFormatError){
        console.error(e.message)
    }else if(e instanceof DateIsInTheFutureError){
        console.info(e.message)
    }else{
        throw e
    }
}
```

## 예외 반환

TypeScript는 `throws` 를 지원하지 않지만 유니온 타입을 이용해 흉내낼 수 있다.

아래와 같이 에러를 명시적으로 한번에 처리 할 수 있다.

```tsx
function parse(birthday: string): Date | InvalidDateFormatError | DateIsInTheFutureError {
    let date = new Date(birthday)

    if(!isValid(date)){
        return new InvalidDateFormatError('Enter a date in the form YYYY/MM/DD')
    }

    if(date.getTime() > Date.now()) {
        return new DateIsInTheFutureError('Are you in time machine?')
    }
    return date
}

let result = parse(ask())
if(result instanceof Error) {
  console.info(result.message)  
} else {
    console.info('Date is', result.toISOString())
}
```

## Option 타입

특수 목적 데이터 타입을 사용해 예외를 표현하는 방법도 있다.

단점은 해당 데이터 타입을 사용하지 않는 다른 코드와는 호환되지 않는다.

또한 `None`으로 표현했기 때문에 무엇을 실패했는지 표현하지 않는다.

장점으론 에러가 발생할 수 있는 계산에 여러 연산을 연쇄적으로 수행할 수 있다.

가장 많이 사용되는 타입으로는 `Try, Option, Either` 가 있다.

자바스크립트에서 기본 제공되지 않는 타입이기 때문에 NPM에서 설치하거나 직접 구현해야 한다.

`Option`은 어떤 특정 값을 반환하는 대신 값을 포함하거나 포함하지 않을 수도 있는 컨테이너를 반환한다.

```tsx
interface Option<T> {
    flatMap<U>(f: (value: T) => None): None
    flatMap<U>(f: (value: T) => Option<U>): Option<U>
    getOrElse(value: T): T
}

class Some<T> implements Option<T> {
    constructor(private value: T) {}
    flatMap<U>(f: (value: T) => None): None
    flatMap<U>(f: (value: T) => Some<U>): Some<U>
    flatMap<U>(f: (value: T) => Option<U>): Option<U> {
        return f(this.value)
    }
    getOrElse(): T {
        return this.value
    }
}

class None implements Option<never> {
    flatMap(): None {
        return this
    }
    getOrElse<U>(value: U): U {
        return value
    }
}

function Option<T>(value: null | undefined): None
function Option<T>(value: T): Some<T>
function OPtion<T>(value: T): Option<T> {
    if(value == null){
        return new None
    }
    return new Some(value)
}

ask() //Option<String>
    .flatMap(parse) //Option<Date>
    .flatMap(date => new Some(date.toISOString())) //Option<string>
```