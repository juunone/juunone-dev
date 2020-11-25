---
title: Front-End framework
date: 2020-11-26 10:13:50
category: typescript
thumbnail: { thumbnailSrc }
draft: false
---
## 참고

- [O'REILLY](https://www.oreilly.com/library/view/programming-typescript/9781492037644/)

## 학습 목표

- TypeScript를 안전하게 응용 프로그램에 통합하는 방법 이해

## TypeScript 도입

타입스크립트에서 DOM API 를 사용하기 위해서는 간단하게 `tsconfig.json` 설정만 해주면된다.

```tsx
{
	"compilerOptions" : {
		"lib": ["dom", "es2015"]
	}
}

위 설정을 통해 타입스크립트가 타입 검사할때 lib.dom.d.ts (브라우저와 DOM 타입 내장 선언)
파일에 선언된 타입들을 포함하도록 한다.

document.querySelector('.name').innerHTML // 에러 TS2531: 객체가 null 일 수 있다.
```

브라우저에서 동작하는 안전하고 타입에 의해 보호받는 프로그래밍을 하게 도와준다.

## React

리액트에서는 컴포넌트를 타입스크립트로 정의할 수 있고 안전하다.

타입스크립트로 view의 타입들을 결정하게 되면 생산성은 2배로 증가할 수 있다.

TSX = JSX 타입스크립트가 자바스크립트에 제공하는 것과 같은 기능을 제공한다.

컴파일 타임에 타입으로 안전성을 지킬수 있으며, 더 생산적인 코드를 만들 수 있다.

TSX를 사용하기위해 tsconfig.js 에 아래 내용을 추가하면 된다.

```tsx
{
	"compilerOptions" : {
		"jsx": "react"
	}
}
```

jsx는 세가지 모드를 지원한다.

- react
    - JSX를 `.js` 로 컴파일한다.
- react-native
    - 컴파일 하지 않고 JSX를 보존하면서 `.js` 파일을 생성한다.
- preserve
    - JSX 타입을 검사하지만 컴파일 하지않고, `.jsx` 파일을 생성한다.

## TSX 사용하기

리액트에서는 함수 컴포넌트와 클래스 컴포넌트 사용이 가능하다.

프로퍼티를 전달받고 TSX를 렌더링한다.

```tsx
import React, { useState } from 'react'

type Props = {
	isDisabled?: boolean
	size: 'Big' | 'Small'
	text: string
	onClick: React.MouseEvent<HTMLButtonElement>): void
}

export function ToggleButton(props: Props){
	const [toggled, setToggled] = useState(false)
	return <button
					className={`Size-${props.size}`}
					disabled={props.disabled || false}
					onClick={event => {
						setToggled(!toggled)
						props.onClick(event)
					}}> 
						{props.text} 
					</button>
}

let button = <ToggleButton 
							size='Big' 
							text='Sign Up Now' 
							onClick={() => console.log('toggled!')} 
							/>
```
