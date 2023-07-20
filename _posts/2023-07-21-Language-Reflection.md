---
title: "[Java] Reflection"
author: "김소연"
date: 2023-07-21 19:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## Reflection이란?

`Reflection`이란 힙 영역에 로드된 Class 타입의 객체를 통해, 원하는 클래스의 인스턴스를 생성할 수 있도록 지원하고, 인스턴스의 필드와 메소드를 접근 제어자와 상관 없이 사용할 수 있도록 지원하는 자바 API다.

여기서 말하는 로드된 Class는 JVM의 클래스 로더에서 클래스 파일에 대한 로딩을 완료한 후 해당 클래스의 정보를 담은 Class 타입의 객체를 생성하여 메모리의 힙 영역에 저장해 둔 것을 의미한다.
(new 키워드를 통해 만든 객체와 다르다.)

컴파일 시간이 아닌 실행 시간에 동적으로 특정 클래스의 정보를 추출할 수 있는 프로그래밍 기법이라고 할 수 있다.



## 사용방법

### Class 타입의 객체 가져오기

리플렉션을 사용하기 전, 힙 영역에 생성된 Class 타입의 객체를 가져와야한다.

```java
Class<Member> classMember = Member.class;

Member member = new Member();
Class<? extends Member> classMember2 = member.getClass();

Class<?> classMember3 = Class.forName("reflection.Member");
```

- `클래스명.Class`
- `인스턴스.getClass()`
- `Class.forName("FQCN")`
  - FQCN(Fully Qualified Class Name) : 해당 경로와 클래스




### Reflection 사용하기

#### 인스턴스 생성

`Class` 사용해서 생성자를 `Constructor` 타입으로 가져올 수 있다.

`Constructor` 는 `java.lang.reflect` 패키지에서 제공하는 클래스이며, 클래스 생성자에 대한 정보와 접근을 제공한다.

```java
Constructor<?> constructor = classMember.getConstructor();
constructor.setAccessible(true);

Object object = constructor.newInstance();
Member member = (Member)constructor.newInstance();
```

1. Constructor 획득
   `클래스객체.getConstructor()`
2. private 생성자일 경우 접근 허용
   `Constructor객체.setAccessible(true)`

3. 객체를 직접 생성
   `Constructor객체.newInstance()`

- Object 타입으로 받아와지므로 타입 캐스팅을 사용한다.
- private 생성자로 객체를 생성하면 `java.lang.IllegalAccessException` 예외가 발생한다. 이럴 경우 `setAccessable(true)`를 사용하여 해결한다.



#### 인스턴스 필드 사용

```java
Class<Member> classMember = Member.Class;
Member member = new Member("소연", 26);

for(Field field : classMember.getDeclaredFileds()) {
    field.setAccessible(true);
    String fieldInfo = field.getType() + ", " + field.getName() + " = " + field.get(member);
    System.out.println(fieldInfo);
}

Field name = classMember.getDeclaredField("name");
name.setAccessible(true);
name.set(member, "Soyeon");
```

- 인스턴스 변수 필드 목록 가져오기
  `Class객체.getDeclaredFields()`
- 인스턴스 변수 필드 가져오기
  `Class객체.getDeclaredField("필드명")`
- private 필드일 경우 접근 허용
  `Field객체.setAccessible(true)`
- 인스턴스 변수의 값 가져오기
  `Field객체.get(객체)`
- 인스턴스 변수의 값 설정하기
  `Field객체.set(객체, 값)`



#### 인스턴스 메서드 사용

```java
Class<Member> classMember = Member.Class;
Member member = new Member("소연", 26);

Method sayHello = classMember.getDeclaredMethod("sayHello");
sayHello.setAccessible(true);
sayHello.invoke(member);
```

- 인스턴스 메서드 가져오기
  `Method객체.getDeclaredMethod(메소드명, 파라미터 타입.Class)`
- private 메소드일 경우 접근 허용
  `Method객체.setAccessible(true)`
- 인스턴스 메서드 호출 
  `Method객체.invoke(객체);`



## 장점과 단점

### 장점

런타임 시점에서 클래스의 인스턴스를 생성하고, 접근 제어자와 관계 없이 필드와 메소드에 접근하여 필요한 작업을 수행할 수 있는 유연성을 가지고 있다.

### 단점

- 캡슐화를 저해한다.
- 런타임 시점에서 인스턴스를 생성하므로 컴파일 시점에서 해당 타입을 체크할 수 없다.
- 런타임 시점에서 인스턴스를 생성하므로 구체적인 동작 흐름을 파악하기 어렵다.
- 단순히 필드 및 메소드를 접근할 때보다 리플렉션을 사용하여 접근할 때 성능이 느리다. (모든 상황에서 성능이 느리지는 않음.)



## Reflection은 언제 사용할까?

동적으로 클래스를 사용해야할 때 사용한다.

- 프레임워크와 같이 큰 규모의 개발 단계에서는 수많은 객체와 의존 관계를 파악하기 어렵다. Reflection을 사용하면 런타임 시점에서 클래스를 가져와 사용할 수 있다.
- 프레임워크나 IDE에서 동적 바인딩을 이용한 기능을 제공한다.

#### 예시

- IntelliJ의 자동완성 기능
- 스프링 Annotation



## 출처

- https://steady-coding.tistory.com/609
- https://jeongkyun-it.tistory.com/225