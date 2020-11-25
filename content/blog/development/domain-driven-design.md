---
title: DDD (Domain Driven Design)
date: 2020-11-24 16:11:61
category: development
thumbnail: { thumbnailSrc }
draft: false
---

# 참고

- [Minseok medium](https://medium.com/react-native-seoul/%EB%8F%84%EB%A9%94%EC%9D%B8-%EC%A3%BC%EB%8F%84-%EC%84%A4%EA%B3%84-domain-driven-design-in-real-project-1-%EB%8F%84%EB%A9%94%EC%9D%B8-83a5e31c5e45)
- [위키](https://en.wikipedia.org/wiki/Domain-driven_design)
- [cyberx](https://cyberx.tistory.com/57)
- [microsoft blog](https://docs.microsoft.com/ko-kr/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/ddd-oriented-microservice)

# 학습 목표

- 도메인 주도 설계 알아보기

# DDD(Domain Driven Design)

도메인 중심 설계는 소프트웨어 코드의 구조가 비즈니스 도메인 과 일치해야 한다는 개념이다.

DDD 자체가 쉬운 개념은 아니다.

**모든 한 통로는 도메인(Domain) 으로 연결된다.** 

위 내용이 내가 생각한 결론이다.

그래서 이 글은 내 주관이 매우 많이 포함된 글이다.

# "도메인 주도"  여기서 말하는 도메인 이란?

소프트웨어를 이용하는 모든 "사용자" 가 사용하는 것이 도메인 이다.

즉, 나 자신도 사용자 이고, 내부 직원도 마찬가지이다. 

당연하게 일반적으로 아는 유저 들도 사용자라고 부를 수 있다.

# 그래서 무얼 어떻게 하는 걸까?

소프트웨어는 사용자의 요구사항에 따라 계속 변화한다.

원론적으로 소프트웨어가 존재하는 이유는 그걸 계속 사용하고 있는 사용자 때문이다.

개발자가 생각하는 코드의 클래스 이름, 메소드, 변수명 등도 모두 도메인의 한 영역이 될 수 있다.

# DDD가 추구하는 방향

DDD는 모든 생각이 도메인에 집중되어 있다.

비즈니스 매니저는 도메인에 대한 지식이 깊은 사람이고,

개발자는 기술적으로 접근 할 수 있으며, 디자이너는 디자인 관점으로 소프트웨어를 볼 수 있다.

그렇다고 해서 비즈니스 매니저가 새로운 기능을 무조건 개발하자고 요구 할 수도 없다.

기술적으로 가능한지, 디자인이 어떻게 반영 되야 사용성을 높일 수 있을지 라는 부분에 대해서는

지속적으로 협업을 통해 나가야 한다.

# 짧은 결론

각자의 롤에 따른 도메인에 대한 생각을 기반으로 설계하고 Design 하고 기획해서

계속해서 소프트웨어 품질을 올리고 Develop 해 나가야 하는게 도메인 주도 개발이라고 생각한다.

특히 규모가 큰 어플리케이션 일수록 이런 이해관계가 더 긴밀하게 이루어져야 한다.

그래서 요즘엔 개발과 커뮤니케이션을 분리해서 생각하는게 아닌,

개발을 잘하려면 커뮤니케이션도 잘해야 한다.

불필요한 비용을 줄이고, 서로가 말하고자 하는 바를 이해하고 개발을 해야 원하는 결과를 낼 수 있을 것이다.