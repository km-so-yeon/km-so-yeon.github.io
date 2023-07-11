---
title: "[Java] 싱글톤 패턴(Sigleton Pattern)"
author: "김소연"
date: 2023-07-11 19:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## 싱글톤 패턴(Singleton Pattern)이란?

객체의 인스턴스가 오직 1개만 생성되는 패턴입니다.

- 싱글톤 패턴은 **생성 패턴**(Creational Pattern) 중 하나입니다. 생성 패턴은 인스턴스를 만드는 절차를 추상화하는 패턴입니다. 객체를 생성, 합성하는 방법이나 객체의 표현 방법을 시스템과 분리해줍니다.
- 개발을 할 때 단 하나의 인스턴스만을 갖도록 하는 것이 좋은 경우에 사용합니다. 예를 들어 로그를 찍는 객체, 쓰레드 풀, 윈도우 관리자 등 **여러 객체를 관리하는 역할의 객체**는 프로그램 내에서 단 하나의 인스턴스를 갖는 것이 바람직합니다.



## 구현 방법

싱글톤 패턴을 구현하는 방법이 여러 가지가 있는데, 공통적인 특징은 아래와 같습니다.

> - private 생성자만을 정의해서 외부 클래스로부터 인스턴스 생성을 차단합니다.
> - 싱글톤을 구현하고자 하는 클래스 내부에 멤버 변수로써 private static 객체 변수를 만듭니다.
> - public static 메소드를 통해 외부에서 싱글톤 인스턴스에 접근할 수 있도록 접점을 제공합니다.



이러한 특징을 가진 싱글톤 구현 방법들은 
인스턴스 생성 시기, Exception Handling 등에 따라 조금씩 바뀌어 총 6가지가 있습니다.



### 1. Eager Initialization

가장 간단한 형태의 구현 방법으로, 싱글톤 클래스의 인스턴스를 클래스 로딩 단계에서 생성하는 방법입니다.

```java
public class Singleton {
    
    private static final Singleton instance = new Singleton();
    
    private Singleton(){}
    
    public static Singleton getInstance() {
        return instance;
    }
    
}
```

- `private static final Singleton instance = new Singleton();` 
  클래스 로딩 단계에서 인스턴스 생성

- `private Singleton(){}`
  외부 클래스로부터 인스턴스 생성을 차단하는 private 생성자

- `public static Singleton getInstance()`

  외부에서 싱글톤 인스턴스에 접근할 수 있도록 접점을 제공할 수 있도록 하는 public static 메소드



#### 특징

- 어플리케이션에서 해당 인스턴스를 사용하지 않더라도 인스턴스를 생성하기 때문에 자칫 낭비가 발생할 수 있습니다.
- 싱글톤 클래스가 다소 적은 리소스를 다룰 때 사용하여야 합니다. 
  File System, Database Connection 등 큰 리소스들을 다루는 싱글톤을 구현할 때는 getInstance() 메소드가 호출될 때까지 싱글톤 인스턴스를 생성하지 않는 것이 더 좋습니다.
- Exception에 대한 Handling을 제공하지 않습니다.



### 2. Static Block Initialization

Eager Initialization과 유사하지만,
Static Block을 통해 Exception Handling에 대한 옵션을 제공합니다.

```java
public class Singleton {
    
    private static Singleton instance;
    
    private Singleton(){}
    
    static {
        try {
            instance = new Singleton();
        } catch(Exeption e) {
            throw new RuntimeException("Exception occured in creating singleton instance");
        }
    }
    
    public static Singleton getInstance() {
        return instance;
    }
}
```

- `static { try { instance = new Singleton(); ...`
  싱글톤 클래스의 인스턴스를 생성할 때 발생할 수 있는 예외에 대해 처리합니다.



#### 특징

- Eager Initialization과 마찬가지로 클래스 로딩 단계에서 인스턴스를 생성하기 때문에 다소 적은 리소스를 다룰 때 사용하여야 합니다.



### 3. Lazy Initialization

앞의 방식과 다르게 클래스 로딩 시에 인스턴스를 생성하지 않고, 
원할 때 인스턴스를 생성합니다.

```java
public class Singleton {
    
    private static Singleton instance;
    
    private Singleton(){}
    
    public static Singleton getInstance() {
        if(instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
    
}
```

- `public static Singleton getInstance()`
  getInstance() 메소드를 호출할 때 인스턴스가 없다면 생성합니다.



#### 특징

- 앞의 방법들과 다르게 인스턴스를 사용하지 않을 경우 낭비가 발생할 수 있다는 문제를 해결할 수 있습니다.
- 멀티 스레드 환경에서 동기화 문제가 발생할 수 있습니다. 만약 인스턴스가 생성되지 않은 시점에 여러 쓰레드에서 동시에 getInstance()를 호출한다면, 단 하나의 인스턴스를 생성한다는 싱글톤 패턴에 위반하는 문제점이 야기될 수 있습니다. 👉 싱글 스레드 환경이 보장되는 경우에만 사용해야 합니다.



### 4. Thread Safe Singleton

Lazy Initialization의 멀티 스레드 환경에서 동기화 문제를 해결하기 위한 방법으로,
`synchronized` 키워드를 사용하여 오직 하나의 쓰레드만 인스턴스 생성 메소드에 접근할 수 있도록 합니다.

```java
public class Singleton {
    
    private static Singleton instance;
    
    private Singleton(){}
    
    public static synchronized Singleton getInstance() {
        if(instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
    
}
```

- `public static synchronized Singleton getInstance()`
  getInstance() 메소드로 진입하는 쓰레드가 하나로 보장받기 때문에 멀티 쓰레드 환경에서도 정상 동작합니다.



#### 특징

- 멀티 쓰레드 환경에서 오직 하나의 쓰레드만 인스턴스를 생성할 수 있어 동기화 문제를 해결하였습니다.
- 대용량 트래픽이 필요한 어플리케이션에서는 성능이 떨어질 수 있습니다. `synchronized` 키워드를 사용한 `getInstance()` 메소드에 실행하고있는 하나의 쓰레드를 제외하고 다른 쓰레드들에는 Lock이 걸리기 때문입니다.



#### double checked locking

Thread Safe Singleton의 성능 문제를 해결하기 위한 방법으로 
instance가 null일 때만 `synchronized`를 적용하였습니다.

```java
public static Singleton getInstance() {
    if(instance == null) {
        synchronized (Singleton.class) {
            if(instance == null) {
                instance = new Singleton();
            }
        }
    }
    return instance;
}
```



### 5. Bill Pugh Singleton

Bill Pugh가 고안한 방식으로,
inner static helper class를 사용합니다.

```java
public class Singleton {
    
    private Singleton(){}
    
    private static class SingletonHelper {
        private static final Singleton INSTANCE = new Singleton();
    }
    
    public static Singleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
}
```

- `private static class SingletonHelper`
  private inner static class를 두어 싱글톤 인스턴스를 갖게 합니다. SingletonHelper 클래스는 Singleton 클래스가 Load될 때에도 Load되지 않다가 getInstance()가 호출될 때 비로소 JVM 메모리에 로드되고, 인스턴스를 생성하게 됩니다.



#### 특징

- `synchronized` 키워드를 사용하지 않아서 성능 저하 문제를 해결하였습니다.
- 앞선 방식들이 갖고 있는 문제점들을 대부분 해결한 방식을, **현재 가장 널리 쓰이는 싱글톤 구현 방법**입니다.



### 6. Enum Singleton

Java계의 거장 Joshua Bloch가 Enum으로 싱글톤을 구현하는 방법을 제안하였습니다.

```java
public enum EnumSingleton {
    
    INSTANCE;
    
    public static void doSomething() {
        // do something
    }
    
}
```



#### 특징

- 앞의 1~5번 방식들은 Java의 Reflection을 통해 싱글톤을 파괴할 수 있지만 Enum을 사용하여 이를 방지할 수 있습니다.
- 1, 2번 방식과 같이 사용하지 않았을 경우 메모리 낭비를 해결하지 못했습니다.



## 싱글톤 패턴의 장점과 단점

### 싱글톤 패턴의 이점

1. 메모리가 절약되고, 속도가 향상될 수 있습니다.
최초 한 번의 new 연산자를 통해 고정된 메모리 영역(Method area)을 사용하기 때문에 추후 해당 객체에 접근할 때 메모리 낭비를 방지할 수 있습니다. 뿐만 아니라 이미 생성된 인스턴스를 활용하니 속도 측면에서도 이점이 있을 수 있습니다.

2. 데이터 공유가 쉽다.
싱글톤 인스턴스는 전역으로 사용되는 인스턴스이기 때문에 다른 클래스의 인스턴스들이 접근하여 사용할 수 있습니다. 하지만 여러 클래스의 인스턴스에서 싱글톤 인스턴스의 데이터에 동시에 접근하게되면 동시성 문제가 발생할 수 있으니 이 점을 유의해서 설계해야 합니다.

이 외에도 도메인 관점에서 인스턴스가 한 개만 존재하는 것을 보증하고 싶은 경우 싱글톤 패턴을 사용하기도 합니다.

### 싱글톤 패턴의 문제점

1. 싱글톤 패턴을 구현하는 코드가 많이 필요합니다.
정적 팩토리 메서드에서 객체 생성을 확인하고 생성자를 호출하는 경우, 멀티스레드 환경에서 발생할 수 있는 동시성 문제를 해결하기 위해 syncronized 키워드를 사용해야 합니다.

2. 테스트 하기가 어렵습니다.
싱글톤 인스턴스는 자원을 공유하고 있기 때문에 테스트를 격리된 환경에서 수행되려면 매번 인스턴스의 상태를 초기화시켜주어야 합니다. 그렇지 않으면 어플리케이션 전역에서 상태를 공유하기 때문에 테스트가 온전하게 수행되지 못합니다.

3. 의존 관계상 클라이언트가 구체 클래스에 의존하게 됩니다.
new 키워드를 직접 사용하여 클래스 안에서 객체를 생성하고 있으므로, 이는 SOLID 원칙 중 DIP를 위반하게 되고, OCP 원칙 또한 위반할 가능성이 높습니다.

이 외에도 자식클래스를 만들 수 없다는 점과, 내부 상태를 변경하기 어렵다는 점 등 여러가지 문제들이 존재합니다. 결과적으로 싱글톤 패턴은 유연성이 많이 떨어지는 패턴이라고 할 수 있습니다.



## 출처

- [[생성 패턴\] 싱글톤(Singleton) 패턴을 구현하는 6가지 방법 :: 준비된 개발자 (tistory.com)](https://readystory.tistory.com/116)
- https://tecoble.techcourse.co.kr/post/2020-11-07-singleton/