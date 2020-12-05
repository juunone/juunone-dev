---
title: extends, implements 차이
date: 2020-12-05 21:12:95
category: typescript
thumbnail: { thumbnailSrc }
draft: true
---

# 참고

- [stackoverflow](https://stackoverflow.com/questions/10839131/implements-vs-extends-when-to-use-whats-the-difference)
- [https://velog.io/@hkoo9329/자바-extends-implements-차이](https://velog.io/@hkoo9329/%EC%9E%90%EB%B0%94-extends-implements-%EC%B0%A8%EC%9D%B4)
- [https://shlee0882.tistory.com/106](https://shlee0882.tistory.com/106)
- [https://rok93.tistory.com/entry/Java-extends-implements-abstract-차이](https://rok93.tistory.com/entry/Java-extends-implements-abstract-%EC%B0%A8%EC%9D%B4)

# 학습 목표

- `extends` 와 `implements` 차이 이해 및 `abstract`추상 클래스 개념 이해

# extends 예약어

상속 이라는 개념의 대표적인 형태이다.

객체 지향에 대해 설명한 글에서 추상화 개념과 상속의 개념을 설명했는데

상속의 한 '형태' 라고 생각하면 된다.

부모에서 선언된 메소드 혹은 변수를 자식은 모두 상속 받아 그대로 사용 할 수 있다.

```tsx
class Phone {
  protected version: string = "ios14";

  public getVerson() {
    return this.version;
  }

  public setVerson(version: string) {
    this.version = version;
  }
}

class IPhone extends Phone {
  public printVersion() {
    console.log(this.version);
  }
}

let phone = new IPhone();
console.log(phone.printVersion()); // ios14
```

`protected` 접근 제어자는 상속 받은 곳  혹은 동일한 클래스 내부에서 사용 가능하다.

`IPhone`클래스가 `Phone`클래스를 상속받고 `printVersionag` 함수로 `version` 을 출력했다.

참고로 `TypeScript`는 다중 상속을 지원하지 않는다. 

`class IPhone extends Phone, Apple` 와 같이 선언하면 다음과 같이 에러를 보여 준다. 

**"Classes can only extend a single class"**

# implements 예약어

implements 는 부모의 메소드나 변수를 그대로 가져다 쓰는게 아닌 반드시 **오버라이드** 해서 사용 해야 한다.

해당 인터페이스에 있는 프로퍼티 및 메소드를 전부 가지고 있거나 구현해야 한다.

그리고 다중 상속도 지원한다.

e.g. `class Dog implements Animal, Friend {...}`

```tsx
interface Animal {
  dog: string;
  getName(): void;
}

class Dog implements Animal {
  constructor(public dog: string) {}

  getName(): void {
    console.log(this.dog);
  }
}

function callWith(dog: Animal): void {
  dog.getName();
}
callWith(new Dog("jindo"));
```

# abstract 예약어

**추상 메소드는 내용이 없이 메소드 이름과 타입만이 선언된 메소드를 말한다.**

`abstract`로 표시된 추상 클래스 내의 메소드에는 `Implementation`이 포함되어 있지 않으므로 파생 클래스에서 구현해야 한다.

추상 클래스(abstract class)는 하나 이상의 추상 메소드를 포함하며 일반 메소드도 포함할 수 있다.

직접 인스턴스를 생성할 수 없고 상속 만을 위해 사용된다.

 추상 클래스를 상속한 클래스는 추상 클래스의 추상 메소드를 반드시 구현하여야 한다.

```tsx
abstract class Department {
  constructor(public name: string) {}
  abstract printName(): void; // must be implemented in derived classes
}

class Hyundai extends Department {
  printName(): void {
    console.log(this.name);
  }
}
```