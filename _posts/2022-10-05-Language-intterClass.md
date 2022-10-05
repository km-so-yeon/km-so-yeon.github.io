---
title: "[Java] 내부클래스"
author: "김소연"
date: 2022-10-05 21:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## 내부 클래스(Inner Class)

클래스 내에 선언된 클래스이다.

- 생성 이유 : 두 클래스가 서로 긴밀한 관계에 있기 때문
- 장점
  1. 내부 클래스에서 외부 클래스의 멤버들을 쉽게 접근할 수 있다.
  2. 코드의 복잡성을 줄일 수 있다.(캡슐화)

```java
class A {		// 외부 클래스 (outer class)
  class B {		// 내부 클래스 (inner class)
    
  }
}
```

내부 클래스 B는 외부 클래스인 A를 제외하고 다른 클래스에서 잘 사용되지 않는 것이어야 한다.



#### 종류와 특징

- 선언위치에 따라 선언위치의 변수와 동일한 유효범위(scope)와 접근성(accessibility)를 갖는다.

| 내부클래스                          | 특징                                       |
| ------------------------------ | ---------------------------------------- |
| 인스턴스 클래스<br />(instance class) | - 외부 클래스의 멤버변수 선언위치에 선언<br />- 외부 클래스의 인스턴스멤버처럼 다루어진다.<br />- 주로 외부 클래스의 인스턴스멤버들과 관련된 작업에 사용될 목적으로 선언된다. |
| 스태틱 클래스<br />(static class)    | - 외부 클래스의 멤버변수 선언위치에 선언<br />- 외부 클래스의 static멤버처럼 다루어진다.<br />- 주로 외부 클래스의 static멤버, 특히 static메서드에서 사용될 목적으로 선언된다. |
| 지역 클래스<br />(local class)      | - 외부 클래스의 메서드나 초기화 블럭 안에 선언<br />- 선언된 영역 내부에서만 사용될 수 있다. |
| 익명 클래스<br />(anonymous class)  | - 클래스의 선언과 객체의 생성을 동시에하는 이름없는 클래스<br />- 일회용 |

```java
class Outer {
  class InstanceInner {}		// 인스턴스 클래스
  static class StaticInner {}	// 스태틱 클래스
  
  void myMethod() {
    class LocalInner {}			// 지역 클래스
  }
}
```



#### 내부 클래스의 제어자와 접근성

내부 클래스도 클래스이기 때문에 **제어자**(abstract, final)를 사용할 수 있고, 
멤버변수들처럼 **접근제어자**(private, protected)도 사용 가능하다.

- 스태틱 클래스만 static멤버를 가질 수 있다.

```java
class Outer {
  class InstanceInner {
    int iv = 100;
    //static int cv = 100; 			// static변수를 선언할 수 없다.
    final static int CONST = 100;	// final static은 상수이므로 허용한다.
  }
  
  static class StaticInner {
    int iv = 200;
    static int cv = 200;			// static클래스만 static멤버를 정의할 수 있다.
  }
  
  void myMethod() {
    class LocalInner {
      int iv = 300;
      //static int cv = 300;		// static변수를 선언할 수 없다.
      final static int CONST = 300;	// final static은 상수이므로 허용한다.
    }
  }
}
```

- 인스턴스 메서드에서는 인스턴스멤버와 static멤버 모두 접근 가능하지만
  스태틱 메서드에서는 static멤버만 접근 가능하고 인스턴스멤버를 직접 접근할 수 없다. (객체 생성 후 접근 가능)

```java
class Outer {
  class InstanceInner {}
  static class Static Inner {}
  
  static void staticMethod() {
  	// static메서드는 인스턴스멤버에 직접 접근할 수 없다.
    //InstanceInner ii = new InstanceInner();
    StaticInner si = new StaticInner();
    
    // 굳이 접근하려면 객체를 생성해야한다.
    // 인스턴스클래스는 외부 클래스를 먼저 생성해야만 생성할 수 있다.
    Outer outer = new Outer();
    InstanceInner ii = outer.new InstanceInner();
  }
  
  void instanceMethod() {
  	// 인스턴스메서드에서는 인스턴스멤버와 static멤버 모두 접근 가능하다.
    InstanceInner ii = new InstanceInner();
    StaticInner si = new StaticInner();
    
    // 메서드 내에 지역적으로 선언된 내부 클래스는 외부에서 접근할 수 없다.
    //LocalInner li = new LocalInner();
  }
  
  void myMethod() {
    class LocalInner{}
    
    LocalInner li = new LocalInner();
  }
}
```

- 내부 클래스에서 외부 클래스의 변수들에 대한 접근성
  - 인스턴스 클래스는 인스턴스 변수, static 변수를 모두 사용할 수 있다. (private이어도 사용 가능)
  - 스태틱 클래스는 static 변수만 사용할 수 있다.
  - 지역 클래스는 인스턴스 멤버와 static 멤버, 지역 클래스가 포함된 메서드의 지역변수 모두 사용할 수 있다. 
    - 지역변수는 final이 붙은 상수만 가능 : 메서드가 수행을 마쳐서 지역변수가 소멸된 시점에도 지역 클래스의 인스턴스가 소멸된 지역변수를 참조하려는 경우가 발생할 수 있기 때문이다. 
      (JDK 1.8부터 final 생략 가능 - 지역 클래스에서 접근하는 지역 변수 앞에 final을 컴파일러가 자동으로 붙여준다.)

```java
class Outer {
  private int outerIv = 0;
  static int outerCv = 0;
  
  class InstanceInner {
    int iiv = outerIv;
    int iiv = outerCv;
  }
  
  static class StaticInner {
    //int siv = outerIv;
    static int scv = outerCv;
  }
  
  void myMethod() {
    int lv = 0;
    final int LV = 0;	// JDK1.8부터 final 생략가능
    
    class LocalInner {
      int liv = outerIv;
      int liv2 = outerCv;
      
      //int liv3 = lv;
      int liv4 = LV;
    }
  }
}
```

- 외부 클래스가 아닌 다른 클래스에서 내부 클래스를 생성하고 내부 클래스의 멤버에 접근할 경우
  - 이런 경우 내부 클래스로 선언해서는 안되는 클래스를 내부 클래스로 선언했다는 의미이다.
  - 컴파일 했을 때 생성되는 파일명 : 외부 클래스명$내부 클래스명.class
    - 각각 다른 메서드에 같은 이름의 지역 클래스가 있을 경우 : 외부 클래스명$**숫자**내부클래스명.class

```java
class Outer {
  class InstanceInner {
    int iv = 100;
  }
  static class StaticInner {
    int iv = 200;
    static int cv = 300;
  }
  
  void myMethod() {
    class LocalInner {
      int iv = 400;
    }
  }
}

class Other {
  // 인스턴스 클래스의 인스턴스를 생성하려면
  // 외부 클래스의 인스턴스를 먼저 생성해야 한다.
  Outer oc = new Outer();
  Outer.InstanceInner ii = oc.new InstanceInner();
  
  // 스태틱 클래스는 외부 클래스를 먼저 생성하지 않아도 된다.
  Outer.StaticInner si = new Outer.StaticInner();
  
  System.out.println("ii.iv : " + ii.iv);
  System.out.println("Outer.StaticInner.cv : " + Outer.StaticInner.cv);
  System.out.println("si.iv" + si.iv);
}

// 컴파일 시 생성되는 파일명
// Other.class
// Outer.class
// Outer$InstanceInner.class
// Outer$StaticInner.class
// Outer$LocalInner.class
```

- 내부 클래스와 외부 클래스에 선언된 변수의 이름이 같을 때
  - 외부 클래스의 변수 : 외부클래스명.this.변수명
  - 내부 클래스의 변수 : this.변수명

```java
class Outer {
  int value = 10;	// Outer.this.value
  
  class Inner {
    int value = 20;	// this.value
    
    void myMethod() {
      int value = 30;
      System.out.println("value : " + value);						// 30
      System.out.println("this.value : " + this.value);				// 20
      System.out.println("Outer.this.value : " + Outer.this.value);	// 10
    }
  }
}
```



#### 익명 클래스

```java
new 조상클래스이름() {}
또는
new 구현인터페이스이름() {}
```

- 이름이 없다. (생성자도 가질 수 없다.)
- 클래스의 선언과 객체의 생성을 동시에 한다.
- 단 한번만 사용될 수 있고 오직 하나의 객체만을 생성할 수 있는 일회용 클래스이다.
- 단 하나의 클래스를 상속받거나 단 하나의 인터페이스만을 구현할 수 있다.
  - 조상클래스 이름이나 구현클래스 이름으로 정의하기 때문이다.

```java
class Outer {
  Object iv = new Object() { void method(){} };			// 익명 클래스
  static Object cv = new Object() { void method(){} };	// 익명 클래스
  
  void myMethod() {
    Object lv = new Object() { void method(){} };		// 익명 클래스
  }
}
```







### 출처📎

- 자바의 정석