---
title: JavaScript 함수 호출 방식
date: 2020-12-08 11:12:40
category: javascript
thumbnail: { thumbnailSrc }
draft: false
---

# 참고

- [https://blueshw.github.io/2018/09/15/pass-by-reference/](https://blueshw.github.io/2018/09/15/pass-by-reference/)
- [https://codeburst.io/explaining-value-vs-reference-in-javascript-647a975e12a0](https://codeburst.io/explaining-value-vs-reference-in-javascript-647a975e12a0)
- [https://www.educative.io/courses/step-up-your-js-a-comprehensive-guide-to-intermediate-javascript/7nAZrnYW9rG](https://www.educative.io/courses/step-up-your-js-a-comprehensive-guide-to-intermediate-javascript/7nAZrnYW9rG)
- [https://wayhome25.github.io/cs/2017/04/11/cs-13/](https://wayhome25.github.io/cs/2017/04/11/cs-13/)

# 학습 목표

- JavaScript 함수 호출 방식 파악

## JavaScript 의 원시 자료형

[기본 자료형 (Primitive)](https://developer.mozilla.org/ko/docs/Glossary/Primitive)

- [Boolean](https://developer.mozilla.org/ko/docs/Glossary/%EB%B6%88%EB%A6%B0)
- [Null](https://developer.mozilla.org/en-US/docs/Glossary/Null)
- [Undefined](https://developer.mozilla.org/ko/docs/Glossary/undefined)
- [Number](https://developer.mozilla.org/en-US/docs/Glossary/Number)
- [String](https://developer.mozilla.org/ko/docs/Glossary/String)
- [Symbol](https://developer.mozilla.org/ko/docs/Glossary/Symbol) (ECMAScript 6 에 추가됨)

그 외 object, array, function 모두 객체

# call by value (값에 의한 호출)

함수 호출 시 메모리에 함수를 위한 임시 공간이 생긴다.

함수 호출 시 전달받은 인자 값을 복사한다.

인자 값이 복사 됐기 떄문에 전달된 인자 값과 전달 받은 파라미터 주소 값은 다르다.

```jsx
let a = 10;

function multiply(num){
	num = num * 10;
};

multiply(a);

console.log(a) //10
```

변수  `a`는 Number 원시 자료형이다.

`multiply` 함수를 통해서 a값을 10배로 곱해 주려고 인자로 전달했지만 결과는 '10' 그대로이다.

값에 의한 호출이 일어났기 때문에

1. multiply 함수 호출시 a 변수의 속성 주소값 복사
2. muliply 함수 인자 num은 a 변수의 속성 주소값과 다름  `num의 값 !== a의 값`

즉, 함수 인자 전달시 주소 값이 복사 됐기 때문에 원본 값에는 영향을 미치지 않는다.

## 재할당을 통한 이해

```jsx
let obj = {a:1};

let newObj = obj;
console.log(obj === newObj); //true

newObj = {b:1};
console.log(obj === newObj); //false
```

1. `obj`를 `newObj`에 할당
2. `obj === newObj` 둘 모두 같은 주소값을 가졌기 떄문에 같은 객체로 인식
3. `newObj` 값을 재할당
4. `obj !== newObj` newObj에 값이 재할당됨으로서 주소값의 변경이 일어남 이제 둘은 같은 객체로 볼 수 없다.

# 인자의 속성 값 변경만 일어났을 경우

```jsx
let a = {b:10};

function multiply(obj){
	obj.b = obj.b * 100;
};

multiply(a);

console.log(a) //{b:1000}
```

1. `a` 변수에 `{b:10}` 할당
2. `multiply` 함수에 `a`를 인자로 전달
3. multiply 함수에서 전달받은 obj  파라미터는 a 와 주소값이 같다. 즉 `obj === a`
4. `obj.b` 속성에 값 재할당
5. `obj` 와 같은 주소 값을 바라보던 `a`의 속성도 변경됨. `a = {b:1000}`

# 재 할당과 속성 값 비교

```jsx
//속성 값만 변경된 경우
const obj = {
    innerObj: {
        x: 9
    }
};
 
const z = obj.innerObj;
 
z.x = 25; //obj.innerObj === z의 주소값은 같은 상태에서 값만 재할당이 일어남으로서 둘다 값이 변경됨.
 
console.log(obj.innerObj.x); //25 obj.innerObj === z

//재할당이 일어난 경우
const obj = {
    arr: [{ x: 17 }]
};
 
let z = obj.arr;
 
z = [{ x: 25 }]; // z 값의 주소값이 변경됨으로서 z !== obj.arr
 
console.log(obj.arr[0].x); //17 obj.arr !== z
```