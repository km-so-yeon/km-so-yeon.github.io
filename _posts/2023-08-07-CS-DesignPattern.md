---
title: "[CS] Design pattern 디자인패턴"
author: "김소연"
date: 2023-08-07 19:00:00 -0500
categories: [CS]
tags: [CS]
---



## Design pattern 디자인패턴

실제 개발 환경에서 비즈니스 요구사항을 프로그래밍으로 처리하면서 만들어진 다양한 해결책 중 많은 사람들이 인정한 베스트 프랙티스를 정리한 것이다.

- 객체지향 특성과 설계원칙을 기반으로 구현되어 있다.
- 스프링은 OOP 프레임워크이다. 그렇기 때문에 스프링을 공부하다보면 디자인 패턴을 만날 수 있다.



## 어댑터 패턴(Adapter Pattern)

**호출당하는 쪽의 메서드를 호출하는 쪽의 코드에 대응하도록** 
**중간에 변환기를 통해 호출하는 패턴**이다.

- 어댑터 : 변환기라는 뜻

- 서로 다른 두 인터페이스 사이에 통신을 가능하게 한다.
- 예시 : JDBC/ODBC, 플랫폼별 JRE



### 구현 방법

- 변환기에서 객체를 참조변수로 만들어서 참조한다.
- 변환기에서 각 객체의 메서드를 같은 이름의 메서드로 호출해서 사용할 수 있게 해준다.

```java
public class ServiceA {
    void runServiceA() {
        System.out.println("ServiceA");
    }
}
```

```java
public class ServiceB {
    void runServiceB() {
        System.out.println("ServiceB");
    }
}
```

```java
public class AdapterA {
    ServiceA sa1 = new ServiceA();
    
    void runService() {
        sa1.runServiceA();
    }
}
```

```java
public class AdapterB {
    ServiceB sb1 = new ServiceB();
    
    void runService() {
        sb1.runServiceB();
    }
}
```

```java
public class ClientWithAdapter {
    public static void main(String[] args) {
        AdapterA aa1 = new AdapterA();
        AdapterB ab1 = new AdapterB();
        
        // 동일한 메서드명
        aa1.runService();
        ab1.runService();
    }
}
```





### 충족하는 설계원칙

- 개방 폐쇄 원칙(OCP)을 활용한 설계 패턴이다.





## 프록시 패턴(Proxy Pattern)

**<u>제어 흐름을 조정</u>하기 위한 목적으로 중간에 대리자를 두는 패턴**이다.

- 프록시 : 대리자, 대변인 이라는 뜻
- 클라이언트 쪽에서는 실제 서비스 객체를 통해 메서드를 호출하고, 대리자 객체를 통해 메서드를 호출하고 반환값을 받는지 전혀 모르게 처리할 수 있다.



### 구현 방법

![designPattern](/assets/img/designPattern.jpg){:width="80%" height="80%"}

- 대리자는 실제 서비스와 같은 이름의 메서드를 구현한다. 이 때 인터페이스를 사용한다.
- 대리자는 실제 서비스에 대한 참조 변수를 갖는다. (합성)
- 대리자의 실제 서비스와 같은 이름의 메소드를 호출하면, 그 값을 클라이언트에게 돌려준다.
- 대리자는 실제 서비스와 메서드 호출 전 후에 별도의 로직을 수행할 수도 있다.

```java
public interface InterfaceService {
    public abstract String runSomething();
}
```

```java
public class RealService implements InterfaceService {
    public String runSomething() {
        return "실제 로직";
    }
}
```

```java
// 대리자
public class Proxy implements InterfaceService {
	// 실제 서비스에 대한 참조 변수
    InterfaceService iService;
    
    // 실제 서비스와 같은 이름의 메서드
    public String runSomething() {
        // 호출에 대한 흐름제어가 주목적, 반환 결과를 그대로 전달
        iService = new RealService();
        return iService.runSomething();
    }
}
```

```java
public class ClientWithProxy {
    public static void main(String[] args) {
        InterfaceService proxy = new Proxy();
        System.out.println(proxy.runSomething());	// 실제 로직
    }
}
```



### 충족하는 설계원칙

- 개방폐쇄원칙(OCP) : `RealService`와 `Proxy`이 변화되더라도 `ClientWithProxy` 는 큰 영향을 받지 않는다. (`RealService`와 `Proxy`의 확장에는 열려있고 `ClientWithProxy`의 변화에는 닫혀있는 것이다.)
- 의존역전원칙(DIP) : 인터페이스`InterfaceService`를 중간에 두어서 `Proxy`를 바꿔도 `ClientWithProxy`는 영향을 받지 않는다. (구체적인 것에 의존하지 않는다.)





## 데코레이터 패턴(Decorator Pattern)

**메서드 호출의 <u>반환값에 변화</u>를 주기 위해 중간에 장식자를 두는 패턴**이다.

- 데코레이터 : 장식자 라는 뜻
- 원본에 장식을 더하는 패턴이다.



### 프록시 패턴과 비교

프록시 패턴과 구현 방법은 같다. 하지만 프록시 패턴은 반환값을 조작하지 않고 그대로 리턴하는 반면, 데코레이터 패턴은 클라이언트가 받는 반환값에 장식을 덧입힌다.

| 패턴            | 특징                                                         |
| --------------- | ------------------------------------------------------------ |
| 프록시 패턴     | - 제어의 흐름을 변경하거나 별도의 로직 처리를 목적으로 한다. <br />- 클라이언트가 받는 반환값을 특별한 경우가 아니면 변경하지 않는다. |
| 데코레이터 패턴 | - 클라이언트가 받는 반환값에 장식을 더한다.                  |



### 구현 방법

- 장식자는 실제 서비스와 같은 이름의 메서드를 구현한다. 이 때 인터페이스를 사용한다.
- 장식자는 실제 서비스에 대한 참조 변수를 갖는다.
- 장식자의 실제 서비스와 같은 이름의 메서드를 호출하면, 그 반환값에 장식을 더해서 클라이언트에게 돌려준다.
- 장식자는 실제 서비스의 메서드 호출 전후에 별도의 로직을 수행할 수도 있다.

```java
public interface InterfaceService {
    public abstract String runSomething();
}
```

```java
public class RealService implements InterfaceService {
    public String runSomething() {
        return "실제 로직";
    }
}
```

```java
// 장식자
public class Decorator implements InterfaceService {
	// 실제 서비스에 대한 참조 변수
    InterfaceService iService;
    
    // 실제 서비스와 같은 이름의 메서드
    public String runSomething() {
        // 호출에 대한 장식이 주목적, 클라이언트에게 반환 결과에 장식을 더하여 전달한다.
        iService = new RealService();
        return "장식 + " + iService.runSomething();
    }
}
```

```java
public class ClientWithDecolator {
    public static void main(String[] args) {
        InterfaceService decorator = new Decorator();
        System.out.println(decorator.runSomething());	// 장식 + 실제 로직
    }
}
```



### 충족하는 설계원칙

- 프록시 패턴과 동일한 구현 방법을 갖기 때문에 개방폐쇄원칙(OCP), 의존역전원칙(DIP)이 적용된 설계패턴이다.



## 싱글턴 패턴(Singleton Pattern)

**클래스의 인스턴스, 즉 객체를 하나만 만들어 사용하는 패턴**이다.



### 구현 방법

- private 생성자를 갖는다.
- 단일 객체 참조 변수를 정적 속성으로 갖는다.
- 단일 객체 참조 변수가 참조하는 단일 객체를 반환하는 getInstance() 정적 메서드를 갖는다.
- 단일 객체는 쓰기 가능한 속성을 갖지 않는 것이 정석이다.

```java
public class Singleton {
    static Singleton singletonObject;	// 단일 객체 참조 변수(정적 속성)
    
    private Singleton() {};	// private 생성자
    
    // 객체 반환 정적 메서드
    public static Singleton getInstance() {
        if (singletonObject == null) {
            singletonObject = new Singleton();
        }
        
        return singletonObject;
    }
}
```

더 다양한 구현방법은 [이 곳](https://km-so-yeon.github.io/posts/Java-Sigleton-Pattern/)에서 확인할 수 있습니다.



### 특징

- 커넥션 풀, 스레드 풀, 디바이스 설정 객체 등 인스턴스를 여러 개 만들면 불필요한 자원을 사용하게 되고, 프로그램이 예상치 못한 결과를 낳을 수 있는 경우에 사용한다.
- 외부에서 new를 통해 객체를 생성할 수 없다.
- 여러 개의 변수가 하나의 단일 객체를 참조하게 된다.



## 출처

- 스프링 입문을 위한 자바 객체 지향의 원리와 이해
