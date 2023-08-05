---
title: "[Spring] @Transactional - Propagation, Isolation"
author: "김소연"
date: 2023-08-05 19:00:00 -0500
categories: [Spring]
tags: [Spring]
---





스프링 어노테이션인 `@Transactional`의 Propagation과 Isolation에 대해 알아보겠습니다.



## Transaction 트랜잭션이란?

트랜잭션은 데이터베이스의 상태를 변환시키는 하나의 논리적 기능을 수행하기 위한 작업의 단위 또는 한꺼번에 모두 수행되어야 할 일련의 연산들을 의미한다.

- 트랜잭션은 데이터베이스의 상태를 변경시키는 작업의 단위이다.
- 하나의 트랜잭션은 Commit되거나 Rollback된다.



스프링에서는 Transaction 처리를 지원하는데, 프로그래밍적 트랜잭션과 선언적 트랜잭션을 지원한다.

1. 프로그래밍적 트랜잭션

   - TransactionTemplate
   - PlatformTransactionManager

2. 선언적 트랜잭션

   클래스, 메서드 위에 `@Transactional`을 추가해서 관리한다. 



## `@Transactional`

```java
@Transactional(isolation = Isolation.READ_COMMITTED, propagation = Propagation.REQUIRED, rollbackFor = Exception.class)
public int method() throws Exception {
	return sqlMapClient.update("...");
}
```

스프링에서 해당 어노테이션을 적용하면 적용된 클래스 또는 메소드에 트랜잭션이 적용된다.

따라서 로직 흐름에 맞춰 전체적으로 트랜잭션을 적용할 것인지, 아니면 특정 메소드에 적용할 것인지 전략을 잘 세워야 한다.

 `@Transactional` 어노테이션에는 트랜잭션에 propagation, isolation, timeout, read-only, rollback condition 을 설정할 수 있다. 



## propagation

'전파', '전달' 이라는 뜻으로, 트랜잭션을 시작하고 멈추는 **실행 범위를 지정**하는 옵션이다.

트랜잭션을 가져올 때 `TransactionManager`에서 getTransaction을 사용하게 되는데
이 때 propagation 세팅 내용을 기반으로 트랜잭션을 가져온다.



### propagation의 속성

| 옵션명                 | 설명                                                         |
| ---------------------- | ------------------------------------------------------------ |
| REQUIRED<br />(기본값) | **항상 트랜잭션이 실행된다.**<br />기존 트랜잭션이 있으면 해당 트랜잭션을 사용하여 실행되고, 없으면 새로운 트랜잭션을 생성한다. |
| SUPPORTS               | 기존 트랜잭션이 있으면 해당 트랜잭션을 사용하여 실행되고, 없으면 트랜잭션 없이 실행된다. (있으면 쓰고, 없으면 말고) |
| NOT_SUPPORTED          | **항상 트랜잭션 없이 실행된다.**<br />기존 트랜잭션이 있으면 일시중단(보류)시킨다. |
| REQUIRES_NEW           | **항상 새로운 트랜잭션에서 실행된다.**<br />기존 트랜잭션이 있으면 일시중단(보류)시킨다. |
| MANDATORY              | 항상 트랜잭션이 실행된다.<br />- REQUIRED와 비슷하게 기존 트랜잭션이 있으면 이를 이용한다.<br />- 차이점은 기존 트랜잭션이 없으면 새로 시작하는 대신 예외를 발생시킨다.<br />- 혼자서는 독립적으로 트랜잭션을 진행하면 안되는 경우에 사용한다. |
| NEVER                  | 항상 트랜잭션 없이 실행된다.<br />- 기존 트랜잭션도 존재하면 안되고, 있으면 예외를 발생시킨다. |
| NESTED                 | 항상 트랜잭션이 실행된다.<br />- 기존 트랜잭션이 있으면 해당 트랜잭션에 SAVE POINT를 만든다.<br />- 더불어 기존 트랜잭션 내에 중첩 트랜잭션을 만든다. (트랜잭션 안에 다시 트랜잭션 생성)<br />- 이 과정에서 비즈니스 로직에 예외 발생 시, SAVE POINT로 ROLLBACK한다.<br />- 하위(중첩) 트랜잭션은 상위 트랜잭션에 영향을 받지만, 상위 트랜잭션은 하위 트랜잭션에게 영향을 덜 받도록 구축하는 형태이다.<br /><br />기존 트랜잭션이 없는 경우, REQUIRED 처럼 새로운 트랜잭션을 생성한다. |



## isolation

흔히 얘기하는 ACID(Atomicity, Consistency, Isolation, Durability) 특성 중 하나인 isolation이다.

'격리'라는 뜻으로, 해당 속성을 통해 격리 수준을 지정한다.

격리는 **동시에 여러 개의 트랜잭션에 의한 변경사항을 어떻게 적용할지에 대한 옵션**이다.



각각의 isolation level은 아래와 같은 동시성 문제를 0개 혹은 그 이상 예방할 수 있다.

### 동시성 문제

동시에 발생하는 트랜잭션에 의해 발생할 수 있는 문제를 말한다.



#### Dirty Read

변경사항이 반영되지 않은 값을 다른 트랜잭션에서 읽도록 허용할 경우 발생하는 데이터 불일치이다.



#### Non-Repeatable Read

한 트랜잭션 내에서 값을 재조회할 때, 동시성 문제로 인해 같은 쿼리가 다른 값을 반환한다.

즉, 트랜잭션이 끝나기 전에 수정사항이 반영되어, 트랜잭션 내에서 쿼리 결과가 일관성을 가지지 못하는 경우

- 기존 조회결과의 내용을 두 번째에서는 변경된 형태로 조회하게 된다.



#### Phantom Read

다른 트랜잭션에서의 입력 및 삭제 작업을 했을 때, 동일한 쿼리를 재실행하면 다른 행을 반환한다.

- 기존 조회결과에 있던 게 없어지거나, 없던 게 생기게된다.



### isolation 속성

| 옵션명                | 설명                                                         |
| --------------------- | ------------------------------------------------------------ |
| DEFAULT<br />(기본값) | 별도의 값을 설정하지 않는 경우, DBMS의 Isolation level을 따름<br />- DB를 변경하게 되는 경우 주의가 필요함 |
| READ_UNCOMMITED       | 가장 낮은 Isolation level<br />- COMMIT 되지 않은 데이터, 트랜잭션 처리 중인 데이터에 대한 읽기를 허용<br />- 동시성 부작용 모두 발생(Dirty Read, Non-Repeatable Read, Phantom Read)<br />- Postgres은 READ_UNCOMMITED를 허용하지 않고, READ_COMMITED을 사용하도록 함<br />_ Oracle은 READ_UNCOMMITED를 지원하지 않거나 허용하지 않음 |
| READ_COMMITED         | 트랜잭션에서 COMMIT된 확정 데이터만 읽기 허용<br />- 동시성 부작용 중 **Dirty Read를 방지**<br />- Postgres, Oracle, SQL Server의 기본 수준 |
| REPEATABLE_READ       | 트랜잭션이 완료될 때까지 SELECT문이 사용하는 모든 데이터에 Shared Lock 처리<br />- 다른 사용자는 그 영역에 해당하는 데이터에 대한 수정이 불가능하다.<br />- 동시성 부작용 중 **Dirty Read, Non-Repeatable Read 방지**<br /><br />- 선행 트랜잭션이 읽은 데이터는 트랜잭션이 종료될 때까지 후행 트랜잭션이 갱신하거나 삭제하는 것이 불가능하기 때문에 같은 데이터를 두 번 쿼리했을 때 일관성 있는 결과를 리턴한다.<br /><br />- MySQL의 기본수준이고, Oracle은 REPEATABLE_READ를 지원하지 않는다. |
| SERIALIZABLE          | 최고 수준의 Isolation level<br />- **동시성 부작용 모두 방지** (Dirty Read, Non-Repeatable Read, Phantom Read)<br />- 동시 호출을 순차적으로 실행하도록 제한하는 설정이므로, 성능 저하의 우려가 있음<br /><br />- 데이터의 일관성 및 동시성을 위해 MVCC(Multi Version Concurrency Control)을 사용하지 않는다. (MVCC는 다중 사용자 데이터베이스 성능을 위한 기술이며, 데이터 조회 시 LOCK을 사용하지 않고 데이터의 버전을 관리해 데이터의 일관성 및 동시성을 높이는 기술이다.) |





## 출처

- [코딩팩토리 - 트랜잭션이란 무엇인가?](https://coding-factory.tistory.com/226)
- [꿈꾸는 태태태의 공간](https://taetaetae.github.io/2016/10/08/20161008/)
- https://www.baeldung.com/spring-transactional-propagation-isolation
- https://ojava.tistory.com/207