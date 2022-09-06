---
title: "[Java] 다형성(형변환)"
author: "김소연"
date: 2022-09-06 21:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## 다형성(Polymorphism)

- 객체지향에서의 다형성이란 여러 가지 형태를 가질 수 있는 능력이다.

- 자바에서는 한 타입의 참조변수로 여러 타입의 객체를 참조할 수 있도록 하여 다형성을 구현한다.

  👉 **조상클래스 타입의 참조변수로 자손클래스의 인스턴스를 참조할 수 있도록 하는 것**

- 이 때, 자손클래스의 인스턴스에서 조상클래스의 멤버들만 사용할 수 있다.

- 자손클래스의 참조변수로 조상클래스의 인스턴스를 참조하면 컴파일 에러가 발생한다. 
  (존재하지 않는 멤버를 사용할 가능성이 있기 때문에)

```java
class Parent {
  int a;
  int b;
  
  void methodA() { a++; }
  void methodB() { b++; }
}

class Child {
  int c;
  
  void methodC() { c++; }
}
```

![object](/assets/img/Polymorphism.JPG){:width="80%" height="80%"}

```java
Child c = new Child();
Parent p = new Child();
```

![object](/assets/img/Polymorphism2.JPG){:width="80%" height="80%"}



## 참조변수의 형변환

서로 상속관계에 있는 클래스 사이에서만 참조변수의 형변환이 가능하다.

> 자손타입 👉 조상타입 (Up-Casting) : **형변환 생략가능**
>
> 자손타입 👈 조상타입 (Down-Casting) : **형변환 생략불가**

- Down-Casting의 경우 캐스트연산자를 사용하여 명시적으로 형변환을 해주어야 한다.
- 조상의 조상의 클래스로도 형변환이 가능하다. 👉 모든 참조변수는 Object 클래스로 형변환이 가능하다.
- 참조변수의 형변환을 통해 참조하고있는 인스턴스에서 사용할 수 있는 **멤버의 범위(개수)를 조절**하는 것 뿐이다. (형변환은 참조변수의 타입을 변경하는 것이지 인스턴스를 변환하는 것은 아니기 때문에 참조변수의 형변환은 인스턴스에 아무런 영향을 주지 않는다.)
- 자손클래스의 참조변수로 조상클래스의 인스턴스를 형변환하여 참조하면 **실행 시 에러**가 발생한다. 
  컴파일 시에는 참조변수 간의 타입만 체크하기 때문에  실행 시 생성될 인스턴스의 타입에 대해서는 알지 못한다. 그래서 컴파일 시에는 문제가 없었지만 실행 시 에러가 발생하여 비정상적으로 종료된 것이다.



## instanceof 연산자

참조변수가 참조할 수 있는 인스턴스의 타입을 알아보기 위해 사용한다.

>  참조변수 instanceof 타입(클래스명)

- 연산의 결과로 true, false를 반환한다. 
- true는 참조변수가 검사한 타입으로 형변환이 가능하다는 것을 뜻한다. 
  (참조변수와 같은 타입 외에도 조상 타입일 때도 true를 얻게된다.)
- 주로 if문에서 사용된다.



## 참조변수의 메소드, 멤버변수

조상클래스와 자손클래스에 같은 이름의 메서드나 멤버변수를 선언할 경우 (중복 정의할 경우)

- 메서드는 조상클래스의 메서드를 자손클래스에서 오버라이딩한 경우에도 참조변수의 타입과 상관없이 항상 실제 인스턴스의 메서드가 호출되지만,
- 멤버변수는 참조변수의 타입에 따라 다르게 사용된다.
- static메서드는 멤버변수처럼 참조변수의 타입에 따라 다르게 사용된다.



## 매개변수의 형변환

메서드의 매개변수에도 참조변수의 다형성이 적용된다.

```java
class Product {
  int price;
  int bonusPoint;
}

class Tv extends Product {}
class Computer extends Product {}
class Laptop extends Product {}

class Buyer {
  int money = 1000;
  int bonusPoint = 0;
  
  void buy(Product p) {		// 매개변수의 형변환(다형성)
    money = money - p.price;
    bonusPoint = bonusPoint + p.bonusPoint;
  }
}
```

- 매개변수의 타입을 Tv, Computer, Laptop 대신 Product로 하여 매개변수에 다형성을 적용한다.
- 매개변수가 Product타입의 참조변수이기 때문에 메서드의 매개변수로 Product 클래스의 자손타입의 참조변수가 들어올 수 있다.



## 여러 종류의 객체를 배열로 다루기

조상타입의 참조변수 배열을 사용하여, 공통의 조상을 가진 서로 다른 종류의 객체를 배열로 묶어서 다룰 수 있다.

```java
Product p[] = new Product[3];
p[0] = new Tv();
p[1] = new Computer();
p[2] = new Audio();
```



### 출처📎

- 자바의 정석