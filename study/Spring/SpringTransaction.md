---
title: Spring Transaction
description: 
published: true
date: 2024-06-28T08:07:50.500Z
tags: 
editor: markdown
dateCreated: 2024-06-28T08:07:50.500Z
---

# Spring Transaction
# 개요

<aside>
💡 트랜잭션: 더 이상 나눌 수 없는 업무 처리의 최소 단위

</aside>

# 트랜잭션의 특징(**ACID)**

**원자성**

트랜잭션 연산이 모두 반영되거나 모두 반영되지 않아야 한다.

- **커밋(commit)** : 하나의 트랜잭션이 성공적으로 끝나면 트랜잭션을 완료 하고 결과를 반영시킨다.
- **롤백 (rollback)** : 트랜잭션 처리가 비정상적으로 종료된 경우 트랜잭션의 부분적으로 연산한 결과를 취소시킨다.

**일관성**

트랜잭션 결과는 항상 일관성 있어야 한다.

트랜잭션 수행 이전과 이후에 데이터베이스는 일관적인(유효한/정상) 상태를 유지 해야한다.

- 트랜잭션이 데이터베이스의 제약조건을 위반하지 않아야한다.
- 트랜잭션의 작업이 애플리케이션에서 의도하고자 한 작동이 정상적으로 일어나야한다.

**독립성**

여러 트랜잭션은 서로 영향을 주지 않고 독립적으로 실행되는 것처럼 보여야 한다

- isolation level을 통해 독립성을 설정할 수 있다.

**지속성**

완료된 트랜잭션의 결과는 영구적으로 반영되어야 한다.

## **Spring에서 트랜잭션**

Spring에서는 `프로그래밍적 트랜잭션` 관리와 `선언적 트랜잭션` 관리를 지원합니다.

### 1. 프로그래밍적 트랜잭션

프로그래밍적 트랜잭션은 개별 데이터 기술의 트랜잭션 API를 이용해 직접 코드안에서 사용하는 방법입니다.

```java
 	Connection connection = dataSource.getConnection();
	connection.setAutoCommit(false);

	try {
			connection.commit();	
	} catch(SQLException e) {
			connection.rollback();
			throw new RemittanceException();
	}
```

- **트랜잭션 동기화** : 트랜잭션을 시작하기 위한 Connection 객체를 특별한 저장소에 보관해두고 필요할 때 꺼내쓸 수 있도록 하는 기술
    - TransactionSynchronizationManager
- **트랜잭션 추상화** : 트랜잭션 기술의 공통점을 담은 트랜잭션 추상화 기술
    - 각 기술에(JDBC, JPA, Hibernate 등) 종속적인 코드를 이용하지 않고도 일관되게 트랜잭션을 처리할 수 있다.
    - PlatformTransactionManager
    
    
    ### 문제점
    
    - 트랜잭션 처리 코드와 비즈니스 로직이 함께 존재해 개발자가 **핵심관심사**에 집중하기 어렵다.
    - 트랜잭션 처리 코드를 반복적으로 작성 해야한다. (코드 중복)

### 2. 선언적 트랜잭션

선언적 트랜잭션은 AOP를 이용해 코드 외부에서 트랜잭션의 기능을 부여해주고 속성을 지정하는 방법입니다.

선언적 트랜잭션은 프록시 객체를 생성하여 사용되며 XML 또는 어노테이션에 의해 구현됩니다.

AOP를 활용해 트랜잭션을 처리하면 `중복된 트랜잭션 처리 코드를 제거`하고 `비즈니스 로직에 집중된 코드`를 작성할 수 있습니다.

### 선언적 트랜잭션의 개념적 흐름도

1. Target에 대한 호출이 오면, AOP 프록시가 인터셉터 체인을 통해 가로채온 후 Transaction Advisor에게 전달한다.
2. Transaction Advisor는 트랜잭션을 생성한다.
3. Custom Advisor가 있다면, 실행한 후 비즈니스 로직을 호출한다.
4. Transaction Advisor는 커밋 또는 롤백 등의 트랜잭션 결과를 반환한다.


### XML

 AOP(`<aop:config>`)와 트랜잭션 어드바이스(`<tx:advice>`)를 통해 트랜잭션을 적용할 수 있습니다.

어드바이스 객체가 사용할 트랜잭션 관리자(PlatformTransactionManager)를 `transaction-manager` 속성으로 지정합니다.

`<tx:method>` 태그로 트랜잭션을 설정할 메서드 및 속성을 설정할 수 있습니다.

```xml
	<bean id="txManagerOne" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	  <property name="dataSource" ref="dataSourceOne"/>
	</bean>
	  
	<tx:advice id="txAdviceOne" transaction-manager="txManagerOne">
	   <tx:attributes>
	   <tx:method name="selectOnly*" read-only="true"/>
	   <tx:method name="*" propagation="REQUIRED" rollback-for="Exception"/>
	   </tx:attributes>
	</tx:advice>

	<aop:config>
		<aop:pointcut id="requiredTx" expression="execution(* *..*Impl.*(..))"/>
		<!-- <aop:pointcut id="requiredTx" expression="execution(* *..*Impl.*(..)) or execution(* org.egovframe.rte.fdl.excel.impl.*Impl.*(..))"/> -->
		<aop:advisor advice-ref="txAdviceOne" pointcut-ref="requiredTx" />
	</aop:config>
```

### 어노테이션(@Transactional)

`@Transactional`을 클래스 단위 혹은 메서드 단위에 선언하여 트랜잭션을 적용할 수 있습니다.

`@Transactional`이 붙으면 스프링은 해당 타깃을 포인트 컷의 대상으로 자동 등록하며 트랜잭션 관리 대상이 됩니다.

```java
public class UserService{
    private final UserRepository userRepository;   

    @Transactional(readOnly = true)
    public List<User> findAll(User user) {
        return userRepository.save(user);
    }
}
```

## Spring 트랜잭션의 세부 설정(속성)

- **read-only(readOnly) :** 읽기 전용 여부를 설정
- **roll-backfor(rollbackFor)** : 롤백할 예외 타입을 설정
- **no-roll-backfor(noRollbackFor)** : 롤백하지 않을 예외 타입을 설정
- **timeout** : 트랜잭션의 타임아웃 시간을 초 단위로 설정
- **propagation** : 전파 규칙을 설정
    - REQUIRED (기본값) : 이미 진행 중인 트랜잭션이 없으면 **새로 시작하고**, 있다면 기존 트랜잭션에 **참여한다.**
    - REQUIRED_NEW : 트랜잭션 존재 유무와 상관 없이 **항상 새로운 트랜잭션 시작한다.** (독립 보장)
    - MANDATORY : 진행 중인 트랜잭션이 없으면 **예외를 발생**하고, 있으면 **참여한다.** (독립 트랜잭션 생성 안됨을 보장)
    - SUPPORTS : 메소드가 트랜잭션을 필요로 하지는 않지만, 진행 중인 트랜잭션이 존재하면 **참여**, 진행 중인 트랜잭션이 존재하지 않더라도 메소드는 정상적으로 동작한다.
    - NOT_SUPPORTED : 이미 진행중인 트랜잭션이 있으면 이를 보류시키고, 트랜잭션을 사용하지 않도록 한다.
    - NESTED : 이미 실행 중인 트랜잭션이 존재하면, **자식(중첩) 트랜잭션**을 만든다. 부모의 커밋과 롤백에는 영향 받지만, 자식의 결과는 부모에게 영향을 주지 않는다. 실행 중인 트랜잭션이 없다면, 새로운 트랜잭션을 생성해서 사용한다.
    - NEVER : 트랜잭션을 사용하지 않도록 강제한다. 트랜잭션이 존재하면 **예외를 발생**한다.
- **isolation** : 격리 레벨을 설정한다.
    - DEFAULT : 데이터베이스의 기본 설정 사용한다.
    - READ_UNCOMMITED (level 0) : 다른 트랜잭션에서 커밋하지 않은 데이터를 읽을 수 있다.
    - READ_COMMITED (level 1) : 다른 트랜잭션이 커밋하지 않은 정보는 읽을 수 없다. 대신 하나의 트랜잭션이 읽은 로우를 다른 트랜잭션이 수정할 수 있다. 이 때문에 처음 트랜잭션이 같은 로우를 다시 읽을 때 다른 내용이 발견될 수 있다.
    - REPEATABLE_READ (level 2) : 하나의 트랜잭션이 읽은 로우를 다른 트랜잭션이 수정할 수 없도록 막아준다. 하지만 새로운 로우를 추가하는 것은 막지 않는다. 따라서 SELECT로 조건에 맞는 로우를 전부 가져오는 경우 트랜잭션이 끝나기 전에 추가된 로우가 발견될 수 있다.
    - SERIALIZABLE (level 3) : 가장 강력한 트랜잭션 격리 수준으로, 트랜잭션을 순차적으로 진행시켜준다. 가장 안전하지만 가장 성능이 떨어지는 격리수준이기 때문에 극단적으로 안전한 작업이 필요한 경우가 아니라면 사용해서는 안된다.
    
    ## 참조
    
     https://mangkyu.tistory.com/169