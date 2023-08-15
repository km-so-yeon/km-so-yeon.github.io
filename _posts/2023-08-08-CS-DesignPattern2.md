---
title: "[CS] Design pattern 디자인패턴2"
author: "김소연"
date: 2023-08-08 19:00:00 -0500
categories: [CS]
tags: [CS]
---





지난 글에 이어서 다른 디자인 패턴들을 알아보겠습니다.



## 템플릿 메서드 패턴(Template Method Pattern)

상위클래스의 템플릿 메서드에서 하위 클래스에서 오버라이딩한 메서드를 호출하는 패턴



### 구현 방법

- 상위 클래스에서 공통 로직을 수행하는 템플릿 메서드
- 하위 클래스에 오버라이딩을 강제하는 추상 메서드 또는 선택적으로 오버라이딩할 수 있는 훅(Hook) 메서드를 두는 패턴

```java
public abstract class Animal {
   // 템플릿 메서드
   public void playWithOwner() {
      play();   // 추상메서드
      runSomething();   // 훅 메서드
   }

   // 추상 메서드
   abstract void play();

   // Hook(갈고리) 메서드
   void runSomething() {
      System.out.println("runSomething()...");
   }
}
```



#### 템플릿 메서드

- 공통 로직을 수행
- 로직 중에 하위 클래스에서 오버라이딩한 추상메서드/훅 메서드를 호출

#### 추상 메서드

- 템플릿 메서드에서 호출한다.
- 하위 클래스가 **반드시 오버라이딩**해야 한다.

#### 훅 메서드

- 템플릿 메서드에서 호출한다.
- 하위 클래스에서 **선택적으로 오버라이딩** 한다.



### 충족하는 설계원칙

- 의존 역전 법칙(DIP)을 활용하고 있다.



## 팩토리 메서드 패턴(Factory Method Pattern)

오버라이드된 메서드가 객체를 반환하는 패턴

- 팩토리 : 공장이라는 뜻
- 객체 지향에서 팩토리는 객체를 생성한다.
- 팩토리 메서드는 객체를 생성하고 반환하는 메서드를 말한다.
- 팩토리 메서드 패턴은 하위클래스에서 팩토리 메서드를 오버라이딩해서 객체를 반환하게 하는 것이다.



### 구현 방법

```java
public abstract class Animal {
   // 추상 팩터리 메서드
   abstract AnimalToy getToy();
}
```

```java
// 팩터리 메서드가 생성할 객체의 상위 클래스
public abstract class AnimalToy {
   abstract void identify();
}
```

```java
// 팩터리 메서드가 생성할 객체
public class DogToy extends AnimalToy {
   public void identify() {
      System.out.println("DogToy");
   }
}
```

```java
// 팩터리 메서드가 생성할 객체
public class CatToy extends AnimalToy {
   public void identify() {
      System.out.println("CatToy");
   }
}
```

```java
public class Dog extends Animal {
   // 추상 팩토리 메서드를 오버라이딩한다.
   @Override
   AnimalToy getToy() {
      return new DogToy();
   }
}
```

```java
public class Cat extends Animal {
   // 추상 팩토리 메서드를 오버라이딩한다.
   @Override
   AnimalToy getToy() {
      return new CatToy();
   }
}
```

```java
public class CleintWithFactody {
   // 팩토리 메서드를 보유한 객체를 생성한다.
   Animal doggy = new Dog();
   Animal kitty = new Cat();

   // 팩토리 메서드가 객체를 반환한다.
   AnimalToy doggyToy = doggy.getToy();
   AnimalToy kittyToy = kitty.getToy();

   // 팩토리 메소드가 반환한 객체들을 사용한다.
   doggyToy.identify();
   kittyToy.identify();
}
```



### 충족하는 설계원칙

- 의존 역전 원칙(DIP) : 클래스 다이어그램을 보면 의존 역전 법칙을 활용하고 있다.



## 전략 패턴(Strategy Pattern)

클라이언트가 전략을 생성해 전략을 실행할 컨텍스트에 주입하는 패턴



### 구성 요소

- 전략 메서드를 가진 전략 객체
- 전략 객체를 사용하는 컨텍스트(전략 객체의 사용자/소비자)
- 전략 객체를 생성에 컨텍스트에 주입하는 클라이언트(제3자, 전략 객체의 공급자)



### 구현방법

1. 다양한 전략을 공통된 방식으로 사용하기 위해 인터페이스를 정의한다.
2. 인터페이스를 구현하여 다양한 전략 클래스를 만들어낸다.
3. 전략을 사용할 컨텍스트 클래스를 구현한다.
4. 클라이언트는 다양한 전략 중 하나를 선택해 생성한 후 컨텍스트에 주입한다.

```java
// 전략 인터페이스
public interface Strategy {
    public abstract void runStrategy();
}
```

```java
// 전략 인터페이스를 구현하는 클래스
public class StrategyGun implements Strategy {
    @Override
    public void runStrategy() {
        System.out.println("Gun");
    }
}
```

```java
// 전략 인터페이스를 구현하는 클래스2
public class StrategySword implements Strategy {
    @Override
    public void runStrategy() {
        System.out.println("Sword");
    }
}
```

```java
// 전략을 사용할 컨텍스트 클래스
public class Soldier {
    void runContext(Strategy strategy) {
        System.out.println("전투 시작");
        strategy.runStrategy();
        System.out.println("전투 종료");
    }
}
```

```java
// 클라이언트
public class Client {
    public static void main(String[] args) {
        Strategy strategy = null;
        Soldier rambo = new Soldier();
        
		// 총을 군인 람보에게 전달해서 전투를 수행하게한다.
        strategy = new StrategyGun();
        rambo.runContext(strategy);

        // 검을 군인 람보에게 전달해서 전투를 수행하게한다.
        strategy = new StrategySword();
        rambo.runContext(strategy);
    }
}
```



### 특징

- 다양한 문제상황의 해결책으로 사용된다.
- 템플릿 메서드 패턴과 유사하다. 같은 문제의 해결책으로 사용할 수 있다. 템플릿 메서드 패턴은 상속을 이용하고, 전략 패턴은 객체 주입을 이용하여 문제를 해결한다.
- 자바는 단일 상속만 가능하므로 상속이라는 제한이 있는 템플릿 메서드 패턴보다는 전략 패턴을 더 많이 사용한다.



### 설계원칙

- 개방폐쇄원칙(OCP)와 의존역전원칙(DIP)가 적용되었다.



## 템플릿 콜백 패턴(Template Callback Pattern 견본/회신 패턴)

- 전략 패턴의 변형이다. 전략을 익명 내부 클래스로 정의해서 사용한다.
- 스프링 3대 프로그래밍 모델 중 하나인 **DI에서 사용**한다.



```java
public class Client {
    public static void main(String[] args) {
        Soldier rambo = new Soldier();
        
        // 총을 군인 람보에게 전달해서 전투를 수행하게한다.
        rambo.runContext(new Strategy() {
            @Override
            public void runStrategy() {
                System.out.println("Gun");
            }
        });
        
        // 검을 군인 람보에게 전달해서 전투를 수행하게한다.
        rambo.runContext(new Strategy() {
            @Override
            public void runStrategy() {
                System.out.println("Sword");
            }
        });
    }
}
```





### 설계원칙

- 전략 패턴과 동일하게 개방폐쇄원칙(OCP)와 의존역전원칙(DIP)가 적용되었다.



## 출처

스프링 입문을 위한 자바 객체 지향의 원리와 이해