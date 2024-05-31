---
title: Spring AOP
description: 
published: true
date: 2024-05-31T08:14:00.867Z
tags: 
editor: markdown
dateCreated: 2024-05-31T08:14:00.867Z
---

# 개요

<aside>
💡 Aspect-Oriented Programming, 관점 지향 프로그래밍 : 핵심 관심사와 부가 관심사로 나누어서 보고 그 관점을 기준으로 각각 모듈화 하겠다는 프로그래밍 패러다임

</aside>

핵심 관심사 :  각 객체가 가져야 할 본래의 기능

공통 관심사 : 여러 객체에서 공통적으로 사용되는 코드(로그 추적 기능, 트랜잭션 기능 등)

![untitled_(2).png](/untitled_(2).png)

# 상세설명

1. Spring AOP는 로깅, 보안, 트랜잭션 관리 등과 같은 공통적인 관심사를 모듈화 하여 코드 중복을 줄이고 유지 보수성을 향상하는데 도움을 줍니다.
2. **여러 개의 클래스에서 반복해서 사용하는 코드가 있다면 해당 코드를 모듈화 하여 공통 관심사로 분리합니다.** 이를 통해 코드의 재사용성과 유지 보수성을 높일 수 있습니다.

## 용어

Aspect (공통 관심사)

- 여러 객체에서 공통으로 적용되는 기능들을 모듈화 한 것을 의미

JoinPoint (핵심 관심사)

- Advice를 적용할 수 있는 위치 (필드, 메소드)

- 스프링 AOP에서 JoinPoint는 항상 메소드 실행 지점으로 제한

Pointcut

- JointPoint의 상세한 스펙을 정의한 것(적용할 메소드)

Advice

- Aspect의 기능을 정의한 실제 구현체(Class)

Weaving

- Advice를 주기능(핵심 관심사)에 적용하는 행위

## 라이브러리

1. Spring AOP 라이브러리
    - 런타임 위빙을 사용한다
    - 복잡한 설정없이 Spring 빈 등록을 하게되면 자동으로 등록되어 사용하기 매우 편리하다
    - 스프링 빈에만 AOP를 적용할 수 있다
    - 런타임에 바이트코드를 조작하는 만큼, 오버헤드가 심하고 성능에 영향을 크게 미칠 수 있다
2. JAVA AOP 관련 라이브러리 aspectsj
    - 컴파일 시점이나 JVM 클래스 로드시점에 조작(위빙)
    - 컴파일이 완료된 이후 런타임 시점에는 앱 성능에 영향이 없다
    - 생성자 호출, 필드 접근과 같은 다양한 조인 포인트에서 AOP를 적용할 수 있다
    - Spring AOP에 비해 사용 방법이 다양하고 내부 구조가 복잡하다


# AOP 엘리먼트

**`<aop:config>` 엘리먼트** 

- aop 설정 엘리먼트

**`<aop:pointcut>` 엘리먼트**

- pointcut을 지정하기 위한 엘리먼트

```xml
<aop:pointcut id="serviceMethod" expression="execution(* egovframework..*Impl.*(..))" /> 
```

**PointCut 표현식**

![untitled_(3).png](/untitled_(3).png)

**`<aop:aspect>` 엘리먼트**

- `pointcut`과 `Advice`를 결합하기 위해 사용

Advice 동작 시점

- `<aop:before>` : 비지니스 메소드 실행 전 동작
- `<aop:after-returning>`: 비지니스 메소드가 성공적으로 리턴되면 동작
- `<aop:after-throwing>` : 비지니스 메소드 실행 중 예외가 발생하면 동작
- `<aop:after>` : 실행 된 후 동작
- `<aop::after-around>`: 비지니스 메소드 실행 전후에 처리할 로직을 삽입할 수 있음

```xml
<aop:aspect ref="exceptionTransfer">
	<aop:after-throwing throwing="exception" pointcut-ref="serviceMethod" method="transfer" />
</aop:aspect>
```

**`<aop:advisor>` 엘리먼트**

aspect와 같은 기능

몇몇 특수한 경우는 어드바이저를 사용하며 트랜잭션 어드바이스와 함께 사용되는 것을 가장 일반적으로 볼 수 있다

트랜잭션은 커밋이나 롤백 등 어떤 메서드가 호출될지 모르기 때문에 어드바이스 메서드를 지정할 수 없고, aspect가 아닌 advisor로 aop를 설정해야 한다.

```xml
<tx:advice id="txAdviceOne" transaction-manager="txManagerOne">
   <tx:attributes>
   <tx:method name="selectOnly*" read-only="true"/>
   <tx:method name="*" propagation="REQUIRED" rollback-for="Exception"/>
   </tx:attributes>
</tx:advice>

<aop:config>
	<aop:pointcut id="requiredTx" expression="execution(* *..*Impl.*(..))"/>
	<aop:advisor advice-ref="txAdviceOne" pointcut-ref="requiredTx" />
</aop:config>
```

# 참조

---

[Schema-based AOP Support :: Spring Framework](https://docs.spring.io/spring-framework/reference/core/aop/schema.html)