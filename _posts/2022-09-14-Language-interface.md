---
title: "[Java] 인터페이스"
author: "김소연"
date: 2022-09-14 21:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## 인터페이스

- 일종의 추상클래스
- **추상메서드와 상수만 멤버로** 가질 수 있다. (일반 메서드, 멤버변수는 가질 수 없다.)
- 클래스가 설계도, 추상클래스를 미완성 설계도라고 하면 인터페이스는 기본 설계도라고 할 수 있다.



#### 작성

```java
interface 인터페이스이름 {
  public static final 타입 상수이름 = 값;
  public abstract 메서드이름(매개변수목록);
}
```

- 모든 멤버변수는 public static final이어야 하며, 이를 생략할 수 있다.
- 모든 메서드는 public abstract이어야 하며, 이를 생략할 수 있다.
  - 단 static 메서드와 디폴트 메서드는 예외(JDK1.8부터 추가 가능하기 때문)

인터페이스에 정의된 모든 멤버에 예외없이 적용되는 사항이기 때문에 제어자를 생략할 수 있다. 
(생략된 제어자는 컴파일 시 컴파일러가 자동으로 추가해준다.)



#### 상속

- 인터페이스는 인터페이스로부터만 상속받을 수 있다.
- 다중상속이 가능하다. (여러 개 인터페이스로부터 상속받는 것)

```java
interface Movable {
  void move(int x, int y);
}
interface Attackable {
  void attack(Unit u);
}
interface Fightable extends Movable, Attackable {}
```



#### 구현

- 인터페이스 그 자체로는 인스턴스를 생성할 수 없다.


- 클래스에서 인터페이스를 **구현**해야 한다. 

  - `implements` 키워드 사용

  ```java
  class 클래스이름 implements 인터페이스이름 {
    // 인터페이스에 정의된 추상메서드를 구현해야 한다.
  }
  ```

- 인터페이스의 메서드 중 일부만 구현한다면 추상클래스로 선언해야 한다.

  - `abstract` 키워드 사용

  ```java
  abstract class 클래스이름 implements 인터페이스이름 {
    
  }
  ```

- 상속과 구현을 동시에 할 수 있다.

  ```java
  class 클래스이름 extends 조상클래스이름 implements 인터페이스이름 {
    
  }
  ```

- 메서드를 구현할 때(오버라이딩) 조상의 메서드보다 넓은 범위의 접근제어자를 지정해야 한다.

  - 인터페이스 메서드의 생략된 제어자는 `public abstract` 이므로 제어자가 생략되어 있을 경우 반드시 `public`으로 해야한다.

  ​




### 출처📎

- 자바의 정석