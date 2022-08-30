---
title: "[Java] 상속"
author: "김소연"
date: 2022-08-29 21:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## 상속 (Inheritance)

기존의 클래스를 재사용하여 새로운 클래스를 작성하는 것

- 보다 적은 양의 코드로 새로운 클래스를 작성할 수 있다.
- 코드를 공통적으로 관리할 수 있기 때문에 코드의 추가 및 수정이 매우 용이해진다.

#### 사용방법

`extends` 사용

```java
class Parent {}
class Child extends Parent {}
```

- Child 클래스에 Parent클래스의 멤버변수와 메서드가 추가된다.
- 상속받는다는 것은 조상클래스를 확장(extends)한다는 의미로 해석할 수 있다.
- 초기화블럭은 상속되지 않는다.
- 접근제어자로 private, default가 사용된 멤버들은 상속되지 않는다기보다는 자손클래스로부터 접근이 제한되는 것으로 보는 것이 옳다.

#### 용어

| 상속해주는 클래스 | 상속받는 클래스 |
| --------- | -------- |
| 부모클래스     | 자식클래스    |
| 상위클래스     | 하위클래스    |
| 기반클래스     | 확장된클래스   |



## 포함관계

한 클래스의 멤버변수로 선언하여 다른 클래스를 포함시키는 것

```java
class Point {
  int x;
  int y;
}

class Circle {
  Point c = new Point();	// 원점
  int r;
}
```

- 다른 클래스를 재사용하여 간결하고 손쉽게 클래스를 작성할 수 있다.
- 단위클래스별로 코드가 작성되어 있어서 코드를 관리하기 수월하다.

#### 포함관계/상속관계 구분

>상속관계 : ~은 ~이다 (is-a)
>
>포함관계 : ~은 ~을 가지고 있다 (has-a)



## 단일상속 (Single Inheritance)

- 자바에서는 단일 상속만을 허용한다. (하나 이상의 클래스로부터 상속을 받을 수 없다.)
- 인터페이스를 이용해서 보완된 형태의 다중상속을 구현할 수 있다.

#### 다중상속을 허용할 경우

- 장점
  - 복합적인 기능을 가진 클래스를 쉽게 작성할 수 있다.
- 단점
  - 클래스의 관계가 매우 복잡해진다.
  - 서로 다른 클래스로부터 상속받은 멤버들의 이름이 같은 경우 구별할 수 있는 방법이 없다.





## Object 클래스

모든 클래스의 조상

- 모든 클래스 상속계층도의 제일 위에 위치하는 조상클래스이다.
- 다른 클래스로부터 상속받지않는 모든 클래스들은 object클래스를 상속받게함으로써 이것을 가능하게 한다. (컴파일 시 컴파일러가 `extends Object` 추가)




## 오버라이딩 (Overriding)

상속받는 메서드이 내용을 변경하는 것

- 메서드를 자손클래스에 맞게 변경해야하는 경우 사용한다.
- 사전적 의미는 '덮어쓰다(overwrite)' 혹은 '~에 우선하다' 이다.

#### 조건

메서드의 선언부가 조상클래스의 메서드와 완전히 일치해야 한다.

- **이름, 매개변수, 리턴타입**이 일치해야 한다.
- 접근제어자는 조상메서드과 같거나 넓은 범위이어야 한다. 
  (넓은 범위 순 : public, protected, defualt, private)
- 예외는 조상메서드보다 적거나 같아야 한다.
  (선언된 예외의 개수보다 던질 수 있는 예외의 개수를 고려해야 한다.)

#### 오버로딩과 오버라이딩의 차이

- 오버로딩(Overloadindg) : 기존에 없는 새로운 메서드를 정의하는 것 (new)
- 오버라이딩(Overriding) : 상속받은 메서드의 내용을 변경하는 것 (change)

```java
class Parent {
  void parentMethod() {}
}

class Child extends Parent {
  void parentMethod() {}		// 오버라이딩
  void parentMethod(int i) {}	// 오버로딩
  
  void childMethod() {}
  void childMethod(int i) {}	// 오버로딩
  void childMEthod() {}			// 에러 (중복정의 되었음)
}
```



## super, super()

#### super

조상클래스를 가리키는 참조변수

- 자손클래스에서 조상클래스로부터 상속받은 멤버를 참조할 때 사용한다.


- 상속받은 멤버와 자신의 클래스에 정의된 멤버의 이름이 같을 때 super를 사용해서 구별할 수 있다.
- static 메서드는 인스턴스와 관련이 없기 때문에 static 메서드에서 사용할 수 없고 인스턴스 메서드에서만 사용할 수 있다. (this와 마찬가지)

```java
class Parent {
  int x = 10;
}

class Child extends Parent {
  int x = 20;
  void method() {
    System.out.println(x);			// 20
    System.out.println(this.x);		// 20
    System.out.println(super.x);	// 10
  }
}
```

#### super()

조상클래스의 생성자

- 조상 클래스의 생성자를 호출할 때 사용한다.

- 자손클래스의 인스턴스 생성 시 자손의 멤버와 조상의 멤버가 하나의 인스턴스로 생성된다.
  그래서 자손클래스의 인스턴스가 조상클래스의 멤버들을 사용할 수 있는 것이다.

- 생성자의 첫 줄에서 조상클래스를 호출해야 한다.

  자손클래스의 멤버가 조상클래스의 멤버를 사용할 수도 있기 때문에 조상의 멤버들이 먼저 초기화되어 있어야 한다.

- 조상클래스 생성자의 호출은 클래스의 상속관계를 거슬러 올라가면서 계속 반복된다.
  마지막으로 모든 클래스의 최고 조상인 Object 클래스의 생성자`Object()` 까지 가서 끝이난다.
  그래서 Object를 제외한 모든 클래스의 생성자는 첫 줄에 반드시 자신의 다른 생성자 또는 조상의 생성자를 호출해야 한다. 

  - 그렇지 않으면 컴파일러는 생성자의 첫 줄에 자동적으로 `super();`를 추가한다. 
  - 생성자가 정의되어있는 클래스에는 컴파일러가 기본 생성자를 자동적으로 추가하지 않는다.

```java
class Point {
  int x = 10;
  int y = 20;
  
  Point(int x, int y) {
    this.x = x;
    this.y = y;
  }
}

class Point3D extends Point {
  int z = 30;
  
  Point3D() {
    this(100, 200, 300);	// Point3D(int x, int y, int z) 호출
  }
  
  Point3D(int x, int y, int z) {
    super(x, y);			// Point(int x, int y) 호출
    this.z = z;
  }
}
```

(Point 클래스 생성자가 정의되어있기 때문에 호출해야 함)



### 출처📎

- <자바의 정석>