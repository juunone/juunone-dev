---
title: 블랙박스 테스트 기법
date: 2020-10-01 18:10:99
category: tdd
thumbnail: { thumbnailSrc }
draft: false
---

# 참고

- [김코딩님 기술 블로그](https://huns.me/posts/2020-01-09-35)
- [슈어소프트 블로그](http://blog.naver.com/PostView.nhn?blogId=suresofttech&logNo=220845981593&parentCategoryNo=&categoryNo=101&viewDate=&isShowPopularPosts=false&from=postView)
- [wiki](https://ko.wikipedia.org/wiki/%EB%B8%94%EB%9E%99%EB%B0%95%EC%8A%A4_%EA%B2%80%EC%82%AC)
- [guru99](https://www.guru99.com/black-box-testing.html)

# 학습 목표

- 블랙박스 테스트 기법 이해하기

# 블랙박스 테스트 기법이란?

![Black-Box-Testing](https://user-images.githubusercontent.com/35126809/94793048-da1b2080-0414-11eb-9cc3-6ee31e5b1824.png)
> 출처: [system-testing](https://datawider.com/what-is-system-testing-and-types-of-system-testing/)

블랙박스 테스트란 '명세' 와 '경험' 을 기반으로 하는 테스트이다.

소프트웨어의 구조나 원리를 모르는 상태에서도 가능한 테스트라고 볼 수 있다.

개발자 혹은 테스트를 진행하는 QA에서 사용자 관점으로 테스트 하는 방식이다.

## 장점

블랙박스 테스트 기법은 입력 값에 대한 결과만 가지고 비교를 하기 때문에

*BDD : 행위주도개발* 과 일치 하는 부분이 있다.

Given, When , Then 처럼 주어진 환경에서 입력 값이 들어 왔을 때 결과 값이

의도한 값과 일치하는지 확인할 수 있다.

소프트웨어의 원리나 구조를 모르더라도 테스트 명세를 작성할 수 있어서,

테스트가 용이하고, 동일한 형식의 다른 소프트웨어 테스트에도 재사용이 가능하다.

## 종류

- 등가 분할
- 경계 값 분석
- 결정 테이블
- 상태 전이 다이어그램

아래 예시를 들기 위한 기준점을 간단하게 제시 한다.

|기준|결과|
|:---:|:---:|
|1 - 30|'week'|
|31 - 364|'month'|
|365 ~|'year'|
- - -

### 등가 분할(Equivalence partitioning)

입력 값에 따른 동일한 결과 값을 내는 값들을 그룹화 하고,

해당 그룹에 속하는 값으로 결과 값을 비교하는 기법이다.

```jsx
describe("날짜 필터", () => {
  it("날짜가 3이면 결과가 'week' 이어야 한다.", () => {
    const date = 3; //given
    const result = getDateCriteria(date); //when

    expect(grade).toBe("week"); //then
  });

  it("날짜가 62이면 결과가 'month' 이어야 한다.", () => {
    const date = 62; //given
    const result = getDateCriteria(date); //when

    expect(grade).toBe("month"); //then
  });

  it("날짜가 370이면 결과가 'year' 이어야 한다.", () => {
    const date = 370; //given
    const result = getDateCriteria(date); //when

    expect(grade).toBe("year"); //then
  });
});
```

### 경계 값 분석(Boundary value analysis)

입력 조건 중 경계 값에서 에러를 발생시킬 확률이 높다는 점을 이용해서,

입력 조건으로 경계 값을 테스트 케이스로 지정한다.

```jsx
describe("날짜 필터", () => {
  it("날짜가 0이면 결과는 Error를 반환한다.", () => {
    const date = 0; //given
    const result = getDateCriteria(date); //when

		expect(drinkOctopus).toThrowError(new Error("value '0' has no result.")); //then
  });

  it("날짜가 1이면 결과가 'week' 이어야 한다.", () => {
    const date = 1; //given
    const result = getDateCriteria(date); //when

    expect(grade).toBe("year"); //then
  });

  it("날짜가 364이면 결과가 'month' 이어야 한다.", () => {
    const date = 364; //given
    const result = getDateCriteria(date); //when

    expect(grade).toBe("month"); //then
  });
});
```

### 결정 테이블 테스팅(Decision or Branch Table)

입력된 값 들에 대한 결과를 참, 거짓으로 표현해서 여러 경우의 수가 존재하는 경우 유용하다.

```jsx
describe('코스 이수 여부', () => {
	it('모든 스텝을 통과했다면 코스를 이수해야 한다.', () => {
		const course = { //given
			a: true,
			b: true,
			c: true
		};
	
		const isPassed = judgeCourse(course); //when
	
		expect(isPassed).toBeTruthy(); //then
	})

	it('모든 스텝을 통과하지 못했다면 코스를 이수하지 못해야 한다.', () => {
		const course = { //given
			a: true,
			b: true,
			c: false
		};
	
		const isPassed = judgeCourse(course); //when
	
		expect(isPassed).toBeFalsy(); //then
	})
})
```

### 상태 전이 다이어그램

시스템의 상태가 변화함에 따라 상태에 따라 다른 결과물 을 낼 때의 과정을

테스트 케이스로 도출한다.

![state_transition.jpg](https://user-images.githubusercontent.com/35126809/94793049-db4c4d80-0414-11eb-9bbd-6b10530486f3.jpg)
> 출처: [start transition test](https://www.tutorialspoint.com/software_testing_dictionary/state_transition)


|Tests|Test1|Test2|Test3|
|:---:|:---:|:---:|:---:|
|Start State|off|on|on|
|Input|Switch On|Switch Off|Switch Off|
|Output|Light On|Light Off|Fault|
|Finish State|On|Off|On|
- - -

```jsx
describe('코스 이수 여부', () => {
	let switch;
	beforeAll(() => {
		switch = new Switch({state:'off'});
	})

	it('스위치를 키면 불이 켜져야 한다.', () => {
		switch.on(); //given, when
	
		expect(switch.state).toBe('on'); //then
	})

	it('스위치를 끄면 불이 꺼져야 한다.', () => {
		switch.on(); //given

		switch.off(); //when return 'Light Off'
	
		expect(switch.state).toBe('off'); //then
	})

	it('스위치를 끄지 못했다면, 불은 켜 있어야 한다.', () => {
		switch.on(); //given

		switch.off(); //when return 'Fault'
	
		expect(switch.state).toBe('on'); //then
	})
})
```