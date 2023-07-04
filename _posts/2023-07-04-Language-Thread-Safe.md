---
title: "[Java] Thread-Safe"
author: "김소연"
date: 2023-07-04 18:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



스레드는 프로세스의 하위 개념으로써 프로세스에 할당된 자원을 공유한다. 프로그램을 스레드로 분리하여 실행하면 프로세스 내의 자원을 공유하기 때문에 프로세스에 비해 자원 할당 비용이 적게들고 Context switch 비용도 적게 든다.
하지만 자원을 공유하기 때문에 **동기화 문제가 발생**할 수 있다.



## Thread-safe 란?

> 멀티 스레드 프로그래밍에서 일반적으로 어떤 함수나 변수 혹은 객체가 여러 스레드로부터 동시에 접근이 이루어져도 프로그램의 실행에 문제가 없음을 뜻한다. 
> 보다 엄밀하게는 **하나의 함수가 한 스레드로부터 호출되어 실행 중일 때, 다른 스레드가 그 함수를 호출하여 동시에 함께 실행되더라도 각 스레드에서의 함수의 수행 결과가 올바로 나오는 것**으로 정의한다.



## Thread-safe인지 아닌지 알아내는 방법

- 여러 스레드가 동시에 접근 가능한 자원을 사용하는지 여부(전역변수, 힙, 파일 등)
- 핸들과 포인터를 통한 데이터의 간접 접근 여부
- 부수 효과를 가져오는 코드가 있는지 여부



## Java에서 Thread-safety하게 만드는 방법

먼저 고려해야 할 순서대로 나열한다.
위의 방법으로 구현이 어려울 경우 아래의 방법을 사용하면 된다.



### 불변객체 사용하기

불변객체란 객체가 생성된 이후 객체 내의 데이터들이 변할 수 없는 객체를 의미한다.
객체가 초기화 된 이후 변경될 수 없기 때문에 여러 스레드가 동시에 접근해도 동일한 값을 보장받을 수 있다.

```java
public class ImmutableObject {
    private final int value;
    
    public ImmutableObject(int value) {
        this.value = value;
    }
    
    public void getValue() {
        return value;
    }
}
```

기본적으로 모든 멤버변수를 `private`, `final`로 선언해서 `setter`가 없고 값을 변경할 수 없다.
추가적인 작업이 필요한 경우(멤버변수가 참조타입인 경우) [이 곳](https://km-so-yeon.github.io/posts/Language-Immutable-Object/)을 참고하면 된다.



### Stack 한정 프로그래밍

Stack영역만을 한정해서 사용하는 프로그래밍이다.

Stack 내에 할당되는 지역변수를 사용하면 
Stack영역은 각 스레드마다 생성되는 개별 영역이므로 동시성을 보장받을 수 있다.

```java
public class UserStack {
    
    public void useStack(List<String> strList) {
        List<String> localStrList;	// 지역변수
        localStrList.addAll(strList);
        
        for(String str : localStrList) {
            
        }
    }
}
```

넘어오는 매개변수를 복사(addAll)한 뒤에 추가적인 작업을 실행하는데,
localStrList는 Stack 내부에서만 사용하기 때문에 동시성을 보장할 수 있다.



### ThreadLocal 사용하기

위에서 지역변수를 사용해서 동시성을 보장하는 방법은 간결하고 이해하기 쉽지만 
메서드 수행이 끝나고 스택 프레임이 삭제되는 순간 변수의 참조가 없어지기 때문에 다른 곳에서 변수를 사용할 수 없다는 단점이 있다.

이러한 단점을 해결하기 위해 자바에서 `ThreadLocal` 이라는 클래스를 제공한다.

`ThreadLocal` 클래스란 Thread의 정보를 Key로 하는 Map 자료구조이다.
쓰레드 영역에 변수를 설정할 수 있기 때문에, 한 쓰레드가 실행하는 모든 코드에서 그 쓰레드에 설정된 변수 값을 사용할 수 있게 된다.



1. ThreadLocal 클래스를 사용하는 클래스 구현

```java
public class UserThreadLocal {
    public static ThreadLocal<Integer> threadLocalValue = new ThreadLocal<>();
    
    public UserThreadLocal(Integer value) {
        threadLocalValue.set(value);
    }
    
    public printValue() {
        System.out.println(threadLocalValue.get());
    }
    
    public removeThreadLocalValue() {
        threadLocalValue.remove();
    }
}
```

2. 해당 클래스를 사용하여 Thread-safety하게 구현

```java
UserThreadLocal userThreadLocal = new UserThreadLocal(5);
userThreadLocal.printValue();	// 5
```

3. ThreadPool을 사용하여 thread를 재활용할 경우 `remove()` **메서드 사용하여 값을 제거**
   이전에 세팅했던 ThreadLocal의 정보가 남아있어 원치않는 동작을 할 수 있다.

```java
userThreadLocal.removeThreadLocalValue();
```



### java.util.concurrent 패키지 하위 자료구조 사용하기

Java 5에서 추가된 패키지로, 멀티 스레드 환경에서 동기화가 필요할 경우 사용할 수 있는 다양한 유틸리티 클래스들을 제공한다.

그 중에서도 `HashTable`, `ConcurrentHashMap`, `AtomicInteger`, `BlockingQueue` 등의 자료구조 클래스들이 있다. 이러한 클래스들은 데이터들을 다룰 때 `synchronized` 키워드를 사용하여 동시성을 보장한다.

- 자바는 `HashTable`의 성능을 더 증진시키기 위해 JDK 1.5에서 `ConcurrentHashMap`을 도입했다.
  `ConcurrentHashMap`은 조금 더 세밀하게 동시성을 보장하여 더 좋은 성능을 보장한다.



### Lock 사용하기

공유자원을 꼭 사용해야할 경우 해당 자원의 접근을 Lock으로 통제한다.

자바에서 가장 쉽게 Thread-Safe를 보장하는 방법으로, `synchronized` 키워드를 사용하는 방법이다.

메소드 자체를 `synchronized`로 선언하거나, 특정 문장들만 블럭으로 `sychronized` 처리하면
해당 메소드, 블럭에 여러 개의 쓰레드가 접근하더라도 한 순간에는 하나의 쓰레드만 이 메소드를 수행하게 된다.

```java
// 메소드 자체를 synchronized로 선언
public synchronized void plus(int value) {
    amount += value;
}

// 특정 문장들만 synchronized로 선언
public void plus(int value) {
    synchronized(this) {
        amount += value;
    }
}
```

`synchronized`에 대한 더 자세한 내용은 [이 곳](https://km-so-yeon.github.io/posts/Language-Thread/)을 참고하면 된다.



## 결론

이처럼 Java에서 Thread-safe 하게 만드는 방법에 대해서 살펴보았다. 
Java는 기본적으로 멀티스레드 환경이므로  각 스레드에서 데이터가 올바르게 나오는지 확인하여야 하고, 아니라면 이에 맞게 대처해야하기 때문에 잘 알아두어야겠다.




## 출처

- [위키백과 - 스레드_안전](https://ko.m.wikipedia.org/wiki/스레드_안전)
- https://sup2is.github.io/2021/05/03/thread-safe-in-java.html
- https://sabarada.tistory.com/163