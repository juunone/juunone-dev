---
title: TypeScript class and interface
date: 2020-10-10 19:10:86
category: typescript
thumbnail: { thumbnailSrc }
draft: false
---

# 참고

- [O'REILLY](https://www.oreilly.com/library/view/programming-typescript/9781492037644/)
- [TypeScript Handbook](https://typescript-kr.github.io/pages/basic-types.html)

# 학습 목표

- TypeScript에서 클래스를 활용하는 방법
- TypeScript 객체 지향 언어 기능 왜, 어떻게 사용하는지 이해

# 접근 한정자

- public
    - 어디에서나 접근할 수 있고 기본적으로 주어지는 접근 수준이다.
- protected
    - 이 클래스와 서브클래스의 인스턴스에서만 접근할 수 있다.
- private
    - 이 클래스의 인스턴스에서만 접근할 수 있다.
- 인스턴스 프로퍼티 선언할 때 `readonly` 를 추가 할 수 있다.

접근 한정자를 이용해 내부 구현 정보를 공개하지 않고, 잘 정의된 API만 노출 가능하게 클래스를 설계 가능하다.

'체스' 라는 게임을 생각해보면 체스의 말들에 해당하는 인스턴스들을 `Piece` 라는 클래스를 상속받아서 인스턴스를 생성하게 할 수 있다.

```tsx
//체스 말들을 생성하기 위한 API 클래스
abstract class Piece {
	constructor()
}

new Piece('White', 'E', 1) //에러 TS2511: 추상 클래스는 인스턴스를 생성할 수 없다.
```

`abstract` 라는 키워드를 통해서 추상 클래스를 생성 가능하다.

추상 클래스는 해당 클래스를 인스턴스화 할수 없으며, 필요한 메소드를 추상 클래스에 추가 할 수 있다.

```tsx
abstract class Piece {
	// ...
	moveTo(position: Position) {
		this.position = position
	}
	abstract canMoveTo(position: Position): boolean
}
```

- `canMoveTo` 라는 메소드를 추상 메소드이기 때문에 `Piece` 클래스를 상속 받는 모든 클래스는

       반드시 추상 메소드도 같이 구현해야 한다.

- `moveTo` 메소드는 기본 구현을 포함하고 있다. 서브클래스에서 오버라이드할 수 있다.

```tsx
//추상 메소드를 포함한 서브클래스 생성
class King extends Piece {
	canMoveTo(position: Position) {
		// ...
	}
}
```

# Super

JavaScript처럼 TypeScript 도 super 호출을 지원한다.

자식클래스가 부모 클래스에 정의된 메소드를 오버라이드하면 자식인스턴스는 super 를 이용해 부모 버전의

메소드를 호출할 수 있다.

super는 메소드에만 접근 가능하고 프로퍼티에는 접근 불가능하다.

- super.{method}
- super()

# this 반환 타입 사용

```tsx
class Set {
	add(value: number): Set {
		// ...
	}
}

//Set 클래스를 상속받은 서브클래스가 this를 반환하는 모든 메소드의 시그니처를 오버라이드 해야하는 상황
class MutableSet extends Set {
	add(value: number): MutableSet {
		// ...
	}
}

//MutableSet 에서 add 메소드를 오버라이드 할 필요 없다.
class Set {
	add(value: number): this {
		// ...
	}
}
```

# 인터페이스

타입 별칭처럼 인터페이스도 타입에 이름을 지어주는 수단이다.

둘다 타입의 형태를 정의하며 두 형태 정의는 서로 할당 가능하다.

```tsx
type Student = {
	name: string
	grade: number
}

interface IStudent {
	name: string
	grade: number
}

// 공통 영역을 빼서 확장해서 사용할 수 있다.
type Identify = {
	name: string
}

type Student = Identify & {
	grade: number
}

interface IStudent extends Identify {
	grade: number
}
```

## 타입 별칭과 인터페이스의 차이점

1. 타입 별칭의 오른쪽은 타입 표현식(타입 , &, | 등의 연산자)를 포함한 모든 타입이 가능하지만,

    인터페이스는 반드시 형태가 나와야 한다.

    ```tsx
    type A = number
    type B = A | string

    //인터페이스는 위와 같이 작성할 수 없다.
    ```

2. 인터페이스의 타입에 상위 인터페이스를 할당할 수 있는지를 확인한다.

    ```tsx
    interface A {
    	good:(x: number): string
    	bad(x: number): string
    }

    interface B extends A {
    	good(x: string | number): string
    	bad(x: string): string // 'number 타입은 'string' 타입에 할당할 수 없음'
    }
    ```

3. 이름과 범위과 같은 인터페이스가 여러 개 있다면 자동으로 합쳐진다.

    그러나 타입 별칭이 여러 개라면 컴파일 타임 에러가 난다. 이를 '선언 합침' 이라 부른다.

# 구현

클래스를 선언 할때  `implements` 라는 키워드를 이용해 특정 인터페이스를 표현할 수 있다.

인터페이스를 참조하는 클래스는 모든 메소드를 구현해야 하며, 필요하다면 메소드나 프로퍼티를 추가할 수 있다.

```tsx
interface Animal {
	eat(food: string): void
	sleep(hours: number): void
}

class Cat implements Animal {
	eat(food: string) {
		// ...
	}
	sleep(hours: number){
		// ...
	}
}
```

## 인터페이스 vs 추상 클래스

- 인터페이스는 형태를 정의하는 수단이다.
- 인터페이스는 컴파일 타임에만 존재하며 JavaScript 코드를 만들지 않는다.
- 추상클래스는 오직 클래스만 정의할 수 있다.
- 추상클래스는 런타임에 JavaScript 코드를 만들며, 접근 한정자를 지정할 수 있다.
- 여러 클래스에서 공유하는 구현은 추상클래스, 단건으로 쓰는건 인터페이스

# 다형성

```tsx
class MyMap<K, V> {
	constructor(initialKey:K, innitialValue: V) {
		 //...
	}
	get(key: K): V {
		//...
	}
	set(key: K, value: V): void {
		//...
	}
	merge<K1, V1>(map: MyMap<K1, V1>): MyMap<K | K1, V | V1> {
		//...w
	}
	static of<K, V>(k: K, v: V): MyMap<K, V> {
		// ...
	}
}

//참조 https://velog.io/@denmark-banana/TypeScript-%ED%81%B4%EB%9E%98%EC%8A%A4%EC%99%80-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4
class Planet {
  public diameter: number; //지름
  protected isTransduction: boolean = true; //공전
  
  getIsTransduction(): boolean {
    return this.isTransduction;
  }
  
  stopTransduction(): void {
    console.log("stop1");
    this.isTransduction = false;
  }
}

class Earth extends Planet {
  public features: string[] = ["soil", "water", "oxyzen"];
  stopTransduction(): void {
    console.log("stop2");
    this.isTransduction = false;
  }
}

let earth: Planet = new Earth();
earth.diameter = 12656.2;
console.log("1번 : " + earth.diameter); // 12656.2
console.log("2번 : " + earth.getIsTransduction()); //true
earth.stopTransduction(); //stop2
console.log("3번 : " + earth.getIsTransduction()); // false
console.log(earth.features); // 접근불가
```

- 오버라이딩: 상속받은 메소드를 재 선언한다.
- 오버로딩: 함수 이름은 같은데 매개변수 타입이 달라서 여러 개의 함수를 만든다.
    - 호출 될 때 인자 값으로 불러오는 함수(이름은 다 같음)가 달라진다.