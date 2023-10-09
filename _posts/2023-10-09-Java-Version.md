---
title: "[Java] 자바 버전 별 정리 "
author: "김소연"
date: 2023-10-09 19:00:00 -0500
categories: [Language, Java]
tags: [Java]
---





자바 5 버전 이전까지는 1.0 ... 1.4 로 표기했다.  



# J2SE 5 (2004년 9월) 

#### Generics ✨

- **Java의 가장 큰 변화 중 첫번째**
- 타입을 클래스 내부에서 지정하는 것이 아닌 외부에서 사용자에 의해 지정할 수 있게 되었다.  
- 컴파일 시에 타입 체크를 해주기 때문에 타입 안정성이 높아지고 형변환의 번거로움이 줄어들게 되었다.



#### Annotation

- 프로그램의 소스코드 안에 다른 프로그램을 위한 정보를 포함시킨 것
- 주석처럼 프로그래밍 언어에 영향을 미치지 않으면서 다른 프로그램에게 유용한 정보를 제공한다.
  - `@Test`는 테스트 프로그램에서 테스트할 메서드라는 것을 알리는 역할이고, 그 외에 프로그램 자체에 아무런 영향을 미치지 않는다.



#### Enum

- 상수를 편리하게 선언하기 위한 것

```java
enum Direction {
    EAST(1), SOUTH(5), WEST(-1), NORTH(10);
    
    private final int value;
    
    Direction(int value) {this.value = value;}
    
    public int getValue() {return value;}
}
```



#### Auto Boxing/Unboxing 

개발자가 기본형 데이터를 래퍼 클래스로 직접 변환하지 않아도 자동 변환이 된다.   



#### Concurrency API 

병렬 프로그래밍 혹은 멀티 스레드를 손쉽게 구현할 수 있다.  





# Java SE 6(2006년 12월)

표기가 J2SE에서 Java SE로 변경되었다.

#### 가비지 컬렉터 G1 GC

- G1 GC를 오직 테스트 용으로만 사용하도록 추가하였다.  
- G1 GC는 Young, Old 영역을 구분하던 전통적인 GC들과 다르게 물리적으로 구분하지 않는다. 
  일정한 크기의 region으로 구분한다.
  - 각 영역(region)에 객체를 할당하고 GC를 실행한다.





# Java SE 7(2011년 7월)

#### G1 GC 정식으로 포함



#### Diamond Operator Generic 

- 컴파일러에 타입 추론 기능을 추가해서 클래스 초기화 시 적합한 생성자를 결정한다.

  - 7 이전 `List<Integer> list = new ArrayList<Integer>(); `

  - 7 이후 `List<Integer> list = new ArrayList<>();  `





# Java SE 8(2014년 3월)

오라클 인수 후 첫 번째 버전이고, 2개 버전으로 나뉜다. (Oracle JDK, Open JDK) 

#### Lambda ✨

- **Java의 가장 큰 변화 중 두번째**
- 함수의 이름이 없는 함수인 익명 함수이다.
- 모든 메서드는 객체를 생성해야만 메서드를 호출할 수 있는데 이 람다식을 사용해서 메서드의 역할을 할 수 있다.



#### 메서드 참조(Method Reference)

- 람다 표현식이 단 하나의 메소드만을 호출하는 경우 
  람다를 축약해서 표현할 수 있도록 한다. 

  ```java
  클래스이름::메소드이름
  참조변수이름::메소드이름
  ```



#### Stream API

- 컬렉션, 배열, 지정된 범위의 연속된 정수 등 연속되는 것들을 추상화시키고, 이러한 데이터들을 다루는 메서드들을 정의해 놓은 것이다.
- 데이터를 추상화시킨다는 것은 데이터 소스들을 같은 방식으로 다룰 수 있게해서 코드의 **재사용성**이 높아지게 하는 것이다.
- `parallel()` 메소드를 호출해서 **병렬 처리를 쉽게할 수 있다.**



#### 함수형 프로그래밍

- 최근 빅데이터 시대로 들어오면서 병렬 처리가 중요해지고, 이러한 병렬 처리를 위해서 전역 상태를 허용하지 않는 함수형 프로그래밍의 중요성이 커졌다.
  - 함수형 프로그래밍 : **순수함수**(항상 동일한 input, output), **반복문 X**, **고차함수**(함수를 매개변수, 반환값으로 사용), **불변성**

- 이러한 함수형 프로그래밍을 Lambda, Stream API로 구현할 수 있게 되었다.



#### Interface의 Default Methods

디폴트 메소드로 인터페이스에서도 메소드를 구현할 수 있게 되었다. 

인터페이스를 보완해야할 때 추가할 기능이 있을 때 default methods를 추가하면 하위 호환성이 유지하면서 인터페이스를 보완할 수 있다. 

```java
public interface InterfaceTest {
    default void printValue(String val) {
        System.out.println(val);
    }
}

public class ClassTest implements InterfaceTest{
    public void printValue(String val) {
        System.out.println("Override");   
        System.out.println(val);  
    } 
}  

public class Main {
    public static void main(String args[]) {   
        ClassTest obj = new ClassTest();   
        obj.printValue("Hi!!!");  
    } 
} 
```

👉 다이아몬드 문제 발생 



#### Optional

Optional 객체를 제공해서 간편하게 NPE(Null Pointer Exception)에 대응할 수 있다.  



#### 새롭게 추가된 날짜/시간 API 

- javax.time.Clock

- javax.time.ZoneId 

- javax.time.LocalDate 



#### PermGen Area 제거 

Java8 이전에는 초기 설정 시 PermSize와 MaxPermSize를 설정해주어야 했고,
 `java.lang.OutOfMemoryError: PermGen space`에러가 종종 발생했다.

Java8 부터는 Permanent Generation이 Metaspace로 대체되었다. 
PermGen 영역은 JVM에서 관리하지만 Metaspace는 OS에서 관리하는 것으로 바뀌게 된 것이다. 
그래서 Metaspace는 런타임 시 메모리 요구 사항에 따라 자동으로 크기를 조정하며, 필요하다면 MaxMetaspaceSize 매개변수를 설정하여 Metaspace의 양을 조절할 수 있다. 





# Java SE 9(2017년 9월)

#### 모듈시스템 jigsaw 등장

- 기존에 gradle, maven을 사용하여 패키지와 라이브러리를 관리하였지만 서로 다른 패키지 간의 캡슐화가 지원되지 않았다.
- jigsaw는 모듈을 만들고 모듈에 명시적으로 외부에서 호출할 수 있는 API를 선언한다. JDK는 이 기능을 통해 모든 내부 API를 캡슐화해서 구성한다.

- jigsaw project의 목적
  - Java SE 플랫폼의 전반적인 구현 부분과 JDK 부분의 보안성과 유지관리성을 향상시킨다.
  - SE 플랫폼과 JDK를 작은 컴퓨터 디바이스에 보다 쉽게 경량화할 수 있게 한다.



#### A New HTTP Client

- Java SE 8 까지 사용하던 HttpUrlConnection을 대체할 java.net.http 패키지 추가

```java
HttpRequest request = HttpRequest.newBuilder()
  .uri(new URI("https://postman-echo.com/get"))
  .GET()
  .build();

HttpResponse<String> response = HttpClient.newHttpClient()
  .send(request, HttpResponse.BodyHandler.asString());
```



#### Jshell - The Java Shell

- 테스트 프로젝트나 main 메소드 없이 코드를 신속하게 케스트할 수 있는 REPL(Read-Eval-Print-Loop) 도구를 제공한다.



#### Process API 개선

- OS 프로세스 관리 및 컨트롤을 위해 패키지 추가
  - java.lang.ProcessHandle
  - java.lang.ProcessHandle.Info



#### try-with-resources 개선

```java
void tryWithResourcesByJava7() throws IOException {
    BufferedReader reader1 = new BufferedReader(new FileReader("test.txt"));
    try (BufferedReader reader2 = reader1) {
        // do something
    }
}


// final or effectively final이 적용되어 reader 참조를 사용할 수 있음
void tryWithResourcesByJava9() throws IOException {
    BufferedReader reader = new BufferedReader(new FileReader("test.txt"));
try (reader) {
        // do something
    }
}
```



#### 다이아몬드 연산자 익명클래스 사용

- Java SE 7에서 다이아몬드 연산자가 추가되었었는데, 익명 클래스에서는 컴파일 오류가 발생했었다.
  Java SE 9 이후부터는 익명클래스에서도 사용할 수 있도록 개선되었다.

```java
FooClass<Integer> fc = new FooClass<>(1) { 
    // anonymous inner class
};
```



#### Interface Private Method

- 인터페이스 내에서 private 메서드 사용이 가능해졌다.



#### Optional to Stream

- Optional로 Stream을 생성할 수 있게 되었다.

```java
Stream<Integer> stream = Optional.of(1).stream();
```



#### Immutable Collection

- 기존에는 Collections 클래스의 `unmodifiableList()`를 사용해서 불변 객체로 변환시켜주어야 했었다.
- `of()`메서드를 사용하면 불변객체를 리턴한다.



# Java SE 10(2018년 3월)

#### Local-Variable Type Interface `var`

- 로컬 변수 타입 추론 기능이 추가되어서 로컬 변수 타입을 `var`로 선언할 수 있다.



#### Garbage Collector Interface

- 가비지 컬렉터에 대한 인터페이스를 제공한다.



#### Thread-Local Handshakes

- VM-safepoint를 수행할 필요 없이 개별 스레드를 stop시키고 콜백을 수행하도록 하는 기능이다.
- VM-safepoint란 모든 스레드를 일시 정지 시키는 작업이다. (Stop the world)



#### Root Certificates

- root CA 목록을 브라우저와 마찬가지로 Oracle JDK에서 가지고 있는다.
  - CA는 HTTPS 통신에 쓰이는 SSL/TLS 인증서를 발급해주는 인증 기관이다.



# Java SE 11(2018년 9월)

Oracle JDK와 OpenJDK가 통합되었고, Oracle JDK가 유료 모델로 전환되었다.

#### HTTP 클라이언트(JEP 321)

- Java SE 9에 인큐베이팅 형태로 넣었던 HTTP 클라이언트 API를 정식으로 포함했다.
- 이 API는 기존 제공되던 URLConnection 기반의 HTTP 개발보다 개선된 기능과 명명 규칙을 제공한다.
- 특히 HTTP 2.0을 지원해서 웹 소켓 기능도 포함되어 있다.



#### 새로운 String 메서드 추가

- `strip()` 을 사용하여 다양한 공백 문자를 편하게 제거할 수 있다.



# Java SE 12(2019년 3월)

#### switch문 확장

```java
switch(day) {
        case MONDAY, TUESDAY -> System.out.println(6);
        case WEDNESDAY -> System.out.println(7);
}
```



#### String method 추가

- `indent()`, `transform()`, `describeConstable()`, `resolveConstantDesc()`



#### GC 개선





# Java SE 13(2019년 9월)

#### switch문 - yield 예약어 추가

```java
var a = switch(day) {
    case MONDAY, TUESDAY:
        yield 6;
    case WEDNESDAY:
        yield 7;
}
```



#### Text Block

- 줄바꿈 문자 자동으로 포함
- 실험 기능으로 추가되었다.

```java
String str = """
    Hello,
	World
""";
```





# Java SE 14(2020년 3월)

#### switch 표현식 표준화

- Java SE 12, 13에서 preview였던 switch 표현식이 표준화되었다.



#### record

- 데이터를 보유하기 위한 특수한 종류의 클래스이다.
- 모든 필드는 final로 정의되고, 각각 getter를 가지고 있다.
- preview 기능으로 추가되었다.

```java
public record SampleRecord(
	String name,
    Integer age,
    Address address
){}
```



#### 유용한 NullPointerExceptions

- 예외 발생 시 정확히 어떤 변수가 null인지 설명한다.





# Java SE 15(2020년 9월)

#### Text Block

- Java SE 13에서 실험 기능이었고, 15에서 표준화되었다.



####  Sealed Classes

- 상속 가능한 클래스를 지정할 수 있는 봉인 클래스이다.
- 상속 가능한 대상은 상위 클래스 또는 인터페이스 패키지 내에 속해있어야 한다.
- preview 기능으로 추가되었다.

```java
public abstract sealed class Shape
    permits Circle, Rectangle, Square { ...
// 하위 클래스로 허용되는 유일한 클래스들은 Circle, Rectangle, Square이다.
```



#### 그 외

- 패턴 매칭
- ZGC 가비지 컬렉터 추가
- 외부 메모리 접근 API추가
- Solaris 및 SPARC 플랫폼 지원 제외



# Java SE 16(2021년 3월)

#### OpenJDK의 버전 관리 - Git

- 기존에 OpenJDK의 버전 관리를 Mercurial으로 하였으나, Git으로 바뀌었다.
- https://github.com/openjdk/



#### Unix-Domain Socket Channels

- Unix 도메인 소켓에 연결할 수 있다.



#### record

- Java SE 14에서 preview로 추가되었다가 16에서 정식으로 추가되었다.



# Java SE 17(2021년 9월)

#### RandomGenerator (의사난수 생성기)

- 예측하기 어려운 난수를 생성하는 API이다.



#### Sealed Classes

- Java SE 15에서 preview로 제공되었던 기능이 표준화되었다.



#### Pattern Matching for switch

- switch문에서 객체를 전달할 수 있다.
- preview 기능으로 추가되었다.

```java
public String test(Object obj) {
	return switch(obj) {
		case Integer i -> *"Integer"*;
		case String s -> *"String"*;
        case Cat c -> *"Cat"*;
        default -> *"I don't know what it is";
	}
}
```





# Java SE 18(2022년 3월)

#### UTF-8을 Java Standard API의 기본 charset으로 설정



#### Deprecate finalization for Removal

- https://openjdk.org/jeps/421
- `finalize()` 메소드로 Resource들을 정리할 수 있었다.
- 이러한 메소드를 아직 기본적으로 활성화된 상태지만 이후 릴리즈부터 기본적으로 비활성화되고 이후에는 제거될 것이다.



#### 그 외

- simple web server를 위해 command-line tool 제공
- Reimplement Core Reflection with Method Handles
- internet-address resolution SPI



## 출처

- https://hajoung56.tistory.com/35
- [https://goodgid.github.io/Java-8-Default-Method/](https://goodgid.github.io/Java-8-Default-Method/) 
- [velog.io/@ljo_0920/java-버전별-차이-특징](https://velog.io/@ljo_0920/java-%EB%B2%84%EC%A0%84%EB%B3%84-%EC%B0%A8%EC%9D%B4-%ED%8A%B9%EC%A7%95#java-17)
- https://marrrang.tistory.com/16
