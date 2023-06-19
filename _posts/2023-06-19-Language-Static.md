---
title: "[Java] Java에서는 왜 static을 지양해야할까?"
author: "김소연"
date: 2023-06-19 18:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## 왜 static을 지양해야할까?

### 1. 동시성 문제 발생

static 변수는 다른 스레드에서도 접근할 수 있어서 값을 보장하지 않는다.

- 이를 synchronized를 통해 해결할 수 있다. 현재 데이터를 사용하고 있는 스레드를 제외하고 나머지 스레드들은 데이터에 접근할 수 없도록 막는 개념이다. 하지만 대량의 접속이 생기면서 이러한 처리가 많아지게되면 오히려 성능 저하를 일으킬 수 있다.



### 2. 메모리 관련 문제 발생

static 키워드로 선언된 변수는 JVM의 Method area에 할당되어 Garbage collection의 대상이 되지 않는다. 

- Java 7 이전에는 Permanent Generation 영역에 할당되어 메모리 크기를 동적으로 바꿀 수 없었다. 이로 인해 `Out Of Memory Error : PermGen Space` 를 발생시킬 수 있는 문제가 있다.



### 3. 객체지향적이지 않다.

- static 변수는 각 데이터들이 캡슐화되어야 한다는  객체지향 프로그래밍 원칙에 위반된다. 
- static 메서드는 interface를 구현하는 데 사용될 수 없다. 
  즉, 재사용성을 높여주는 Java의 유용한 객체지향적 기능을 사용할 수 없게된다.



## static의 대안들

1. transient 나 volatile같은 제어자
2. 실행속도를 빠르게해주는 메소드 인라이닝을 위한 final 메서드
3. final 메서드 파라미터나 변수를 사용하면 컴파일러에서 최적화 직업이 가능해진다.
4. 싱글톤 디자인 패턴
   - 여러 개의 인스턴스를 만드는 것을 피하고싶을 때 이용



## 출처

- 에프랩 멘토링
- https://unabated.tistory.com/1041