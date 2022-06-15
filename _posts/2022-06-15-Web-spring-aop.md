---
title: "[Spring] 스프링 AOP 개념, 구현 방법"
author: "김소연"
date: 2022-06-15 21:00:00 -0500
categories: [Web Application]
tags: [Spring]
---



## AOP (Aspect Oriented Programming)

관점 지향 프로그래밍

>  똑같은 코드가 여러 곳에 흩어져 있을 경우 수정할 때 여러 번 바꿔줘야 한다. 
>  👉 **공통적인 코드를 한 곳에 모아놓는다.**



```
class A {
  method a() {
  	AAAA
    안녕하세요.
    BBBB
  }
  method b() {
  	AAAA
    반갑습니다.
    BBBB
  }
}

class B {
  method c() {
    AAAA
    안녕히가세요.
    BBBB
  }
}
```

```
class A {
  method a() {
    안녕하세요.
  }
  method b() {
    반갑습니다.
  }
}

class B {
  method c() {
    안녕히가세요.
  }
}

class AAAABBBB {
  method aaaabbbb(JoinPoint point) {
    AAAA
    point.execute()
    BBBB
  }
}
```



## AOP 구현 방법

- 컴파일
- 바이트코드 조작
- 프록시 패턴



#### 컴파일

> A.java --(AOP)--> A.class

- 컴파일할 때 AOP 적용


- java 파일에는 반복되는 코드가 없지만 컴파일한 class 파일에는 있는 것처럼 해준다.


- AspectJ가 제공





#### 바이트코드 조작

> A.java -> A.class --(AOP)--> 메모리

- 클래스를 로딩할 때 AOP 적용
- class 파일에는 반복되는 코드가 없지만 A라는 클래스를 사용할 때 클래스 로더가 읽어와서 메모리에 올릴 때 있는 것처럼 해준다.


- 클로스 로더에다가 특별한 옵션을 붙여서 바이트 코드를 조작한다.
- AspectJ가 제공





#### 프록시 패턴

- 스프링 AOP가 사용하는 방법
- 기존의 코드를 건들이지 않고 바꿀 수 있는 방법이기 때문에 클라이언트에 영향을 적게 준다.

![springaop](/assets/img/aop.png){:width="80%" height="80%"}


**구현 방법**


1. 인터페이스 생성
2. 인터페이스를 구현하는 클래스 생성
3. 인터페이스를 구현할 때 위의 클래스를 사용하여 앞뒤에 코드를 추가하는 클래스 생성 👉 프록시




**사용 방법**

인터페이스를 사용할 때 프록시 인스턴스를 대신 준다.




프록시 구현 예

```java
public interface Payment {
  void pay(int amount);
}
```

```java
public class Cash implements Payment {
  @Override
  public void pay(int amount) {
    System.out.prinln(amount + "현금결제");
  }
}
```

```java
public class CreditCard implements Payment {
  
  Payment cash = new Cash();
  
  @Override
  public void pay(int amount) {
    if (amount > 100) {
      System.out.println(amount + "신용카드");
    } else {
      cash.pay(amount);
    }
  }
}
```



프록시 사용 예

```java
public class Store {
  Payment payment;
  
  public Store(Payment payment){
    this.payment = payment;
  }
  
  public void buySomething(int amount){
    payment.pay(amount);
  }
}
```

```java
public class StoreTest {
  
  @Test
  public void testPay() {
    Payment creditCard = new CreditCard();
    Store store = new Store(creditCard);
    store.buySomething(100);
  }
}
```

Store 클래스에서 Payment를 사용할 때 CreditCard를 주면 cash를 사용할지 말지 판단하는 코드가 실행된다.

- 원래는 Cash가 Bean으로 등록되는데 CreditCard라는 프록시가 대신 등록되어 사용된다.


- CreditCard에서 Cash의 코드를 건드리지 않고 Cash 메소드 앞 뒤에 코드가 추가하여 사용할 수 있다.




@Transactional

- 코드 앞뒤로 트랜잭션 처리를 해주는 코드가 추가되는 Annotation (프록시 패턴 사용)
- 해당 Annotation을 사용하면 Connection에 setAutocommit을 false로 하고 SQL 실행이 모두 끝난 다음 Commit을 하거나 Rollback 해주는 코드가 추가된다. 




### 출처📎


- [인프런 - 예제로 배우는 스프링 입문](https://www.inflearn.com/course/spring_revised_edition#curriculum)
