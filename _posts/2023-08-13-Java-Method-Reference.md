---
title: "[Java] 메소드 레퍼런스(Method Reference, 메소드 참조)"
author: "김소연"
date: 2023-08-13 19:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## 메소드 레퍼런스란?

Java 8에서 추가된 것으로,
메소드 레퍼런스란 람다(Lambda) 표현식을 더 간단하게 표현하는 방법입니다.

람다 표현식이 단 하나의 메소드만을 호출하는 경우 해당 람다 표현식에서 불필요한 매개변수를 제거하고 사용할 수 있도록 해줍니다.

이러한 람다 표현식과 메소드 레퍼런스를 함수형 인터페이스로 받아서 메소드를 실행할 수 있도록 합니다.



```java
Consumer<String> func = text -> System.out.println(text);
func.accept("Hello");
```

위의 람다식에서 System.out.println 메소드는 String 인자를 1개 받아서 void를 리턴합니다.
이를 `System.out::println` 이라는 메소드 레퍼런스로 간단하게 표현할 수 있습니다. 
(Consumer는 객체를 입력받아 void를 출력하는 함수형 인터페이스입니다.)


```java
Consumer<String> func = System.out::println;
func.accept("Hello");
```





## 메소드 레퍼런스 종류

메소드 레퍼런스는 사용하는 패턴에 따라 다음과 같이 분류할 수 있습니다.

| 종류                                                    | 예                                   |
| ------------------------------------------------------- | ------------------------------------ |
| **Static 메소드 참조**                                  | ContainingClass::staticMethodName    |
| 특정 객체의 **Instance 메소드 참조**                    | containingObject::instanceMethodName |
| 특정 유형의 임의의 객체에 대한 **Instance 메소드 참조** | ContainingType::methodName           |
| **Constructor(생성자) 참조**                            | ClassName::new                       |



### Static 메소드 참조

Static Method를 메소드 레퍼런스로 사용하는 것을 말합니다.

`ContainingClass::staticMethodName`



```java
public class StaticMethodReferenceExample {
	public static void main(String[] args) {
        List<String> animals = Arrays.asList("Lion", "Rabbit", "Monkey");
        
        // 람다 표현식
        animals.stream().forEach(animal -> Printer.printSomething(animal));
        
        // 메소드 레퍼런스
        animals.stream().forEach(Printer::printSomething);
    }
    
    public static class Printer {
        static void printSomething(String text) {
            System.out.println(text);
        }
    }
}
```



### Instance 메소드 레퍼런스

객체의 멤버 메소드를 메소드 레퍼런스로 사용하는 것을 말합니다.

- 특정 객체의 **Instance 메소드 참조** : `containingObject::instanceMethodName`

- 특정 유형의 임의의 객체에 대한 **Instance 메소드 참조** : `ContainingType::methodName`

```java
import java.util.function.BiFunction;

public class MethodReferenceExample {
	public static void main(String[] args) {
        MethodReferenceExample myApp = new MethodReferenceExample();
        
        // 람다 표현식
        mergeThigs("Hello ", "World!", (a, b) -> a + b);
        
        // 메소드 레퍼런스 - static 메소드
        mergeThigs("Hello ", "World!", MethodReferenceExample::appendStrings);
        
        // 메소드 레퍼런스 - 특정 객체의 Instance 메소드를 참조할 때
        mergeThigs("Hello ", "World!", myApp::appendStrings2);
        
        // 메소드 레퍼런스 - 특정 유형의 임의의 객체에 대한 Instance 메소드 참조
        mergeThigs("Hello ", "World!", String::concat);
    }
    
    public static <T> T mergeThings(T a, T b, BiFunction<T, T, T> merger) {
        return merger.apply(a, b);
    }
    
    public static String appendStrings(String a, String b) {
        return a + b;
    }
    
    public String appendStrings2(String a, String b) {
        return a + b;
    }
}
```



### Constructor 메소드 레퍼런스

Constructor를 생성해주는 코드입니다.

`ClassName::new`



```java
public class MethodReferenceExample {
	public static void main(String[] args) {
        List<String> animals = Arrays.asList("Lion", "Rabbit", "Monkey");
        
        // 람다 표현식
        animals.stream()
               .map(name -> new Animal(name))
               .forEach(animal -> animal.printName());
        
        // 메소드 레퍼런스
        animals.stream()
               .map(Animal::new)
               .forEach(Animal::printName());
    }
    
    public static class Animal {
        String name;
        public Animal(String name) {
            this.name = name;
        }
        
        public void printName() {
            System.out.println(name);
        }
    }
}
```





## 메소드 레퍼런스를 사용하는 방법

메소드 레퍼런스를 사용하는 방법은 아래와 같습니다.

Static 메소드를 사용하여 설명하겠습니다.



### 1. 함수형 인터페이스 `Executable`를 사용

```java
public class MethodReferenceExample {
	public static void main(String[] args) {
        // 람다 표현식
        Executable exeL = text -> Printer.printSomething(text);
        exeL.doSomething("Lambda");
        
        // 메소드 레퍼런스
        Executable exeM = Printer::printSomething;
        exeM.doSomething("Method Reference");
    }
    
    interface Executable {
        // 함수형 인터페이스 - 추상메서드 하나를 가진다.
        void doSomething(String text);
    }
    
    public static class Printer {
        static void printSomething(String text) {
            System.out.println(text);
        }
    }
}
```

`Printer::printSomething` 를 함수형 인터페이스`Executable`의 `doSomething(String text)` 메소드를 통해 실행시킵니다.



### 2. 자바 8 API에서 제공하는 함수형 인터페이스 사용

```java
import java.util.function.Consumer;

public class MethodReferenceExample {
	public static void main(String[] args) {
        // 람다 표현식
        Consumer<String> exeL = text -> Printer.printSomething(text);
        exeL.accept("Lambda");
        
        // 메소드 레퍼런스
        Consumer<String> exeM = Printer::printSomething;
        exeM.accept("Method Reference");
    }
    
    public static class Printer {
        static void printSomething(String text) {
            System.out.println(text);
        }
    }
}
```

`Printer::printSomething` 를 자바의 함수형 인터페이스 API `Consumer<T>`의 `accept(T t)` 메소드를 통해 실행시킵니다.



### 3. Stream 사용

```java
public class MethodReferenceExample {
	public static void main(String[] args) {
        List<String> animals = Arrays.asList("Lion", "Rabbit", "Monkey");
        
        // 람다 표현식
        animals.stream().forEach(animal -> Printer.printSomething(animal));
        
        // 메소드 레퍼런스
        animals.stream().forEach(Printer::printSomething);
    }
    
    public static class Printer {
        static void printSomething(String text) {
            System.out.println(text);
        }
    }
}
```

`Printer::printSomething` 를 스트림의 forEach문을 통해 animals 리스트의 문자열들을 받아서 실행시킵니다.







## 출처

- 자바의 신
- https://codechacha.com/ko/java8-method-reference/
- https://docs.oracle.com/javase/tutorial/java/javaOO/methodreferences.html
