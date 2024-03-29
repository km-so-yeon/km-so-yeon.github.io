---
title: "[Java] 객체"
author: "김소연"
date: 2022-08-23 21:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## 클래스, 객체, 인스턴스

#### 클래스
- 정의 : 객체를 정의해놓은 것
- 용도 : 객체를 생성하는 데 사용

#### 객체
- 정의 : 실제로 존재하는 것, 사물 또는 개념
- 용도 : 객체가 가지고 있는 기능과 속성에 따라 다름

#### 인스턴스
- 인스턴스화(instantiate) : 클래스로부터 객체를 만드는 과정
- 인스턴스(instance) : 클래스로부터 만들어진 객체



객체는 모든 인스턴스를 대표하는 포괄적인 의미를 갖고 있고, 
인스턴스는 어떤 클래스로부터 만들어진 것인지 강조하는 의미를 가지고 있습니다.

> 클래스 --인스턴스화--> 인스턴스(객체)



## 구성요소
- 객체는 **속성과 기능**으로 이루어져 있습니다.
- 속성과 기능을 객체의 멤버라고 합니다.




#### 객체지향 프로그래밍에서의 용어

- 속성(property) : 멤버변수(variable)
- 기능(function) : 메서드(method)




#### 예시

```java
class Tv {
  // 변수
  String color;
  boolean power;
  int channel;
  
  // 메서드
  void power();
  void channelUp();
  void channelDown();
}
```



## 생성과 사용

#### 생성

```
클래스명 변수명;			// 클래스의 객체를 참조하기 위한 참조변수를 선언
변수명 = new 클래스명();	// 클래스의 객체를 생성한 후, 객체의 주소를 참조변수에 저장
```

#### 사용

```
변수명.멤버				// 참조변수를 통해 인스턴스 멤버에 접근
```

- 인스턴스는 참조변수를 통해서만 다룰 수 있으며, 참조변수의 타입은 인스턴스의 타입과 일치해야 합니다.




#### 여러 개의 인스턴스가 있을 때

- 같은 클래스에서 생성되었더라도 각 인스턴스의 속성은 서로 다른 값을 유지할 수 있고, 
  메서드 내용은 동일합니다.
- 여러 개의 참조변수가 하나의 인스턴스를 가리키는 것은 가능하지만, 
  하나의 참조변수로 여러 개의 인스턴스를 가리키는 것은 불가능합니다.

```java
class TvTest {
  public static void main(String args[]) {
    Tv t1 = new Tv();
    Tv t2 = new Tv();
    
    // 각 인스턴스의 속성은 서로 다른 값을 유지할 수 있다.
    t1.channel = 7;	
    System.out.println(t1.channel);		// 7
    System.out.println(t2.channel);		// 0
    
    // 여러 개의 참조변수가 하나의 인스턴스를 가리키게 된다.
    t2 = t1;
    System.out.println(t1.channel);		// 7
    System.out.println(t2.channel);		// 7
  }
}
```

![object](/assets/img/object.gif){:width="80%" height="80%"}





## 객체 배열

- 참조변수들을 하나로 묶은 참조변수 배열
- 참조변수 배열을 선언한 후 각 요소에 객체를 생성해서 저장해야 합니다.

```java
Tv[] tvArr = new Tv[3];		// 참조변수 배열 생성
tvArr[0] = new Tv();		// 객체를 생성해서 배열의 각 요소에 저장
tvArr[1] = new Tv();
tvArr[2] = new Tv();
```

#### 배열의 초기화 블럭을 사용

```java
Tv[] tvArr = { new Tv(), new Tv(), new Tv() };
```





### 출처📎

- 자바의 정석