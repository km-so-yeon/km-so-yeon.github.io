---
title: "[Java] 제네릭 타입 소거"
author: "김소연"
date: 2023-07-03 18:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## Generic(제네릭)이란

클래스나 메소드에서 사용할 내부 데이터 타입을 컴파일 시 미리 지정하는 방법입니다. [자세한설명](https://km-so-yeon.github.io/posts/Language-Generics/)

그래서 컴파일 타임에 타입 체크를 하기 때문에 런타임에서 `ClassCastException`과 같은 UncheckedException을 보장 받을 수 있다는 장점이 있습니다.

만약 제네릭 클래스를 로타입(Raw Type)으로 타입 파라미터를 지정하지 않고 사용한다면 컴파일 타임에서 타입 체크를 하지 않기 때문에 런타임에서 에러가 발생할 수 있습니다.



#### 제네릭의 컴파일 방법

이러한 제네릭은 JDK 5부터 도입되었습니다. 하지만 JDK 하위 버전에서도 코드가 호환되어야하기 때문에 제네릭을 구현할 때는 소거(erasure) 방식을 사용하였습니다.



## Generics Type Erasure (제네릭 타입 소거)

제네릭의 타입 소거는 컴파일 타임에만 타입에 대한 제약 조건을 적용하고, 
런타임에는 타입에 대한 정보를 소거합니다.

```java
List<Object> list = new ArrayList<Integer>();
list.add("Type Error");
```

그래서 위와 같은 상황에서 컴파일 시 타입 오류를 바로 알 수 있습니다.



Java 컴파일러는 타입 소거를 아래와 같이 적용합니다.

> 1. unbounded Type(\<?\>, \<T\>) 는 타입 파라미터(T)나 Object로 변환한다. 
>    - bound Type(\<E extends Comparable\>) 의 경우 Comparable로 변환한다.
>    - 제네릭 타입을 사용할 수 있는 일반 클래스, 인터페이스, 메소드에만 소거 규칙을 사용한다.
> 2. 타입 안정성 보존을 위해 필요하다면 type casting을 넣는다.
> 3. 확장된 제네릭 타입에서 다형성을 보존하기 위해 bridge method를 생성한다.

아래에서 각각의 규칙을 코드를 통해 설명해보겠습니다.



### 첫 번째 규칙

#### unbounded Type일 때

**타입 소거 전(컴파일 시)**

```java
public class UnboundedClass<T> {
    public <T> List<T> genericMethod(List<T> list) {
        return list.stream.collect(Collectors.toList());
    }
}
```

**타입 소거 후(런타임 시)**

```java
public class UnboundedClass {
    public List<Object> genericMethod(List<Object> list) {
        return list.stream.collect(Collectors.toList());
    }
}
```



#### bound Type일 때

**타입 소거 전(컴파일 시)**

```java
public class BoundClass<T extends Building> {
    public <T extends Building> void genericMethod(T t) {
        
    }
}
```

**타입 소거 후(런타임 시)**

```java
public class BoundClass {
    public void genericMethod(Building t) {
        
    }
}
```



### 두 번째 규칙

```java
UnboundedClass<Integer> genericClass = new UnboundedClass<>();
List<Integer> list = new ArrayList<>();
genericClass.genericMethod(list);
```

개발자가 위와 같이 타입 파라미터를 정해서 제네릭 클래스와 제네릭 메소드를 호출했을 경우
컴파일러 내부에서 두 번째 규칙에 따라 타입 안정성 보존을 위해 타입 캐스트 코드를 넣어줍니다.

(만약 로타입일 경우는 타입 파라미터가 정해져있지 않아 Object로 변환한 것에서 끝납니다.)





### 세 번째 규칙

Bridge method는 java 컴파일러가 컴파일 할 때 메서드 시그니처가 조금 다르거나 애매할 경우를 대비하여 작성된 메서드입니다.

이 경우는 제네릭 클래스나 인터페이스를 확장한 클래스를 컴파일할 때 생길 수 있습니다.

**타입 소거 전(컴파일 시)**

```java
public class IntegerStack extends Stack<Integer> {
    public Integer push(Integer value) {
        super.push(value);
        return value;
    }
}
```

Java 컴파일러는 다형성을 지키기 위해 IntegerStack의 `push(Integer)` 메서드와 Stack의 `push(Object)` 메서드 시그니처 사이에 불일치가 없어야 했다. 따라서 컴파일러는 런타임에 해당 제네릭 타입의 타입소거를 위해 Bridge Method를 아래와 같이 만든다.

**타입 소거 후(런타임 시)**

```java
public class IntegerStack extends Stack {
    // Bridge Method
    public Integer push(Object value) {
        return push((Integer)value);
    }
    
    public Integer push(Integer value) {
        return super.push(value);
    }
}
```

개발자가 IntegerStack의 push() 메소드를 호출하면
 `Integer push(Object value)`  👉 `Integer push(Integer value)` 순서로 호출된다.



## 출처

- https://www.baeldung.com/java-generics

- https://jyami.tistory.com/m/99