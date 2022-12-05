---
title: "[Java] Generics(제네릭)"
author: "김소연"
date: 2022-12-05 21:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## Generic(제네릭)

다양한 타입의 객체들을 다루는 메서드나 컬렉션 클래스에서
컴파일 시 타입 체크(compile-time type check)를 해주는 기능이다.

- **다룰 객체의 타입을 미리 명시해줌으로써, 번거로운 형변환을 줄여준다.**
  - 기존에는 다양한 종류의 타입을 다루는 메서드나 매개변수나 리턴타입으로 Object타입의 참조변수를 많이 사용했고, 그로 인해 형변환이 불가피했지만, 이제는 Object 타입 대신 원하는 타입을 지정하기만 하면 된다.
- JDK1.5에서 처음 도입되었다.



#### 장점

1. **타입 안정성을 제공한다.**
   의도하지 않은 타입의 객체가 저장되는 것을 막고, 저장된 객체를 꺼내올 때 원래의 타입과 다른 타입으로 잘못 형변환 되어 발생할 수 있는 오류를 줄여준다.
2. **타입체크와 형변환을 생략할 수 있으므로, 코드가 간결해진다.**



## Generic Class 선언

1. 클래스 옆에 **\<T>**를 붙인다.
2. Object를 모두 **T**로 바꾼다.

```java
class Box<T> {
  T item;
  
  void setItem(T item) { this.item = item; }
  T getItem() { return item; }
}
```

- T는 타입변수라고 하며, Type의 첫 글자에서 따온 것이다.
  - T가 아닌 다른 것을 사용해도 된다. (예 - Map\<K, V> == Key, Value)
  - 기호만 다를 뿐 임의의 참조형 타입을 의미한다는 것은 모두 같다.



#### 객체 생성

참조변수와 생성자에 타입 T 대신 사용될 실제 타입을 지정해주어야 한다.

```java
Box<String> b = new Box<String>();

b.setItem(new Object());	// 에러. String 이 외의 타입은 지정 불가
b.setItem("ABC");		// OK. String 타입이므로 가능

String item = b.getItem();	// 형변환 필요없음
```



- Generic class인데도 예전의 방식으로 객체를 생성하는 것이 허용된다. 

  ```java
  Box b = new Box();
  b.setItem("ABC");		// 경고. unchecked or unsafe operation
  ```

  - 제네릭 도입 이전의 코드와 호환을 위해 허용하였다.
  - 다만 제네릭 타입을 지정하지 않아서 안전하지 않다는 경고가 발생한다.
    (unchecked or unsafe operation)
  - Object타입을 지정하면, 타입을 지정하지 않은 것이 아니라 알고 적은 것이므로 경고는 발생하지 않는다.
  - **반드시 타입을 지정해서 Generic과 관련된 경고가 나오지 않도록 할 것**










### 출처📎

- 자바의 정석


