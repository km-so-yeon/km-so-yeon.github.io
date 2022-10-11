---
title: "[Java] 예외처리"
author: "김소연"
date: 2022-10-06 21:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## 예외 처리(exception handling)

#### 프로그램 오류

프로그램이 실행 중 어떤 원인에 의해서 오작동을 하거나 비정상적으로 종료되는 경우가 있는데,
이러한 결과를 초래하는 원인을 `프로그램 오류/에러` 라고한다.

- 발생 시점에 따라 구분되는 오류

  | 구분                         | 설명                      |
  | -------------------------- | ----------------------- |
  | 컴파일 에러(compile-time error) | 컴파일 할 때 발생하는 에러         |
  | 런타임 에러(runtime error)      | 프로그램 실행 도중에 발생하는 에러     |
  | 논리적 에러(logical error)      | 실행은 되지만, 의도와 다르게 동작하는 것 |

- 소스코드를 컴파일하면, 컴파일러가 소스코드(.java)에 대해 오타나 잘못된 구문, 자료형 체크 등 기본적인 검사를 수행하여 오류가 있는지 알려준다. 컴파일을 성공적으로 마치면 클래스 파일(.class)이 생성되고, 생성된 클래스 파일을 실행할 수 있게 되는 것이다.

- 자바에서는 런타임 에러를 `에러(error)`와 `예외(exception)` 두 가지로 구분하였다.

  - 에러
    - 일단 발생하면 복구할 수 없는 심각한 오류 (메모리 부족(OutOfMemeryError), 스택오버플로우(StackOverflowError) 등)
    - **프로그램 코드에 의해서 수습될 수 없는 심각한 오류**
    - 비정상적인 종료가 된다.
  - 예외
    - 발생하더라도 수습될 수 있는 비교적 덜 심각한 것
    - **프로그램 코드에 의해서 수습될 수 있는 다소 미약한 오류**
    - 예외가 발생하더라도 프로그래머가 이에 대한 적절한 코드를 미리 작성해놓음으로써 비정상적인 종료를 막을 수 있다.



#### 예외 클래스의 계층 구조

자바에서는 실행 시 발생할 수 있는 오류를 클래스로 정의하였다. (Exception과 Error)

![object](/assets/img/exception.png){:width="80%" height="80%"}

예외 클래스들은 두 그룹으로 나눠질 수 있다. 

![object](/assets/img/exception2.png){:width="80%" height="80%"}

- Exception 클래스들 : **사용자의 실수**와 같은 외적인 요인에 의해 발생하는 예외
  - FileNotFoundException : 존재하지 않는 파일의 이름을 입력했을 때
  - ClassNotFoundException : 실수로 클래스의 이름을 잘못 적었을 때
  - DataFormatException : 입력한 데이터 형식이 잘못되었을 때
- RuntimeException 클래스들 : **프로그래머의 실수**로 발생하는 예외
  - ArrayIndexOutOfBoundsException : 배열의 범위를 벗어났을 때
  - NullPointerException : 값이 null인 참조변수의 멤버를 호출했을 때
  - ClassCastException : 클래스 간의 형변환을 잘못했을 때
  - ArithmeticException : 정수를 0으로 나누려고 했을 때




## 예외처리하기

#### 예외처리

- 정의 : 프로그램 실행 시 발생할 수 있는 예외에 대비한 코드를 작성하는 것
- 목적 : 프로그램의 비정상 종료를 막고, 정상적인 실행상태를 유지하는 것

발생한 예외를 처리하지 못하면,

- 프로그램이 비정상적으로 종료
- 처리하지 못한 예외(uncaught exception)은 JVM의 예외처리기(UncaughtExceptionHandler)가 받아서 예외의 원인을 화면에 출력한다.

#### try-catch문

##### 구조

```java
try {
  // 예외가 발생할 가능성이 있는 문장들을 넣는다.
} catch (Exception1 e1) {
  // Exception1이 발생했을 경우, 이를 처리하기 위한 문장을 적는다.
} catch {Exception2 e2} {
  // Exception2가 발생했을 경우, 이를 처리하기 위한 문장을 적는다.
} catch {Exception3 e3} {
  // Exception3이 발생했을 경우, 이를 처리하기 위한 문장을 적는다.
}
```

- 하나의 try블럭과 하나 이상의 catch블럭

- 발생한 예외의 종류와 일치하는 단 한 개의 cat블럭만 수행된다. (발생한 예외의 종류와 일치하는 catch블럭이 없으면 예외는 처리되지 않는다.)

- if문과 달리, try블럭 혹은 catch블럭 내에 포함된 문장이 하나뿐이어도 괄호`{}`를 생략할 수 없다.

- 중첩되게 사용할 수 있다.

  - catch블럭의 괄호 내에 선언된 변수는 catch블럭 내에서만 유효하므로, 모든 catch블럭에 참조변수 'e' 하나만 사용해도 된다.
  - 중첩된 catch블럭 내에서 같은 이름의 참조변수를 사용해서는 안된다. (두 참조변수의 영역이 서로 겹치므로 다른 이름을 사용해야 구별되기 때문이다.)

  ```java
  try {
    try {} catch (Exception e) {}
  } catch (Exception e) {
    try {} catch (Exception e) {}	// 에러, 변수 e가 중복 선언 되었다.
  }
  ```





##### 흐름

- try 블럭 내에서 예외가 발생한 경우
  1. 발생한 예외와 일치하는 catch블럭이 있는지 확인한다.
  2. 일치하는 catch블럭을 찾으면 그 catch블럭 내의 문장들을 수행하고 
     전체 try-catch문을 빠져나가서 그 다음 문장을 계속해서 수행한다.
     만일 일치하는 catch블럭을 찾지 못하면, 예외는 처리되지 못한다.
- try 블럭 내에서 예외가 발생하지 않은 경우
  1. catch블럭을 거치지 않고 전체 try-catch문을 빠져나가서 수행을 계속한다.




## catch블럭

```java
try {
  System.out.println(1);
  System.out.println(0/0);	// 0으로 나눠서 ArithmeticException 발생
  System.out.println(2);	// 실행되지 않고 넘어간다.
} catch (ArithmeticException ae) {
  System.out.println("ArithmeticException");
  ae.printStackTrace();
  System.out.println("예외메시지 : " + ae.getMessage());
} catch (Exception e) {
  System.out.println("Exception");
}
```

#### Exception클래스

- 맨 마지막의 catch블럭에서 Exception 클래스 타입의 참조변수를 선언해 놓으면,
  어떤 종류의 예외가 발생하더라도 이 catch블럭에 의해서 처리된다.
  - 모든 예외 클래스는 Exception클래스의 자손이기 때문이다.


#### 예외 정보 출력

- `printStackTrace()` : 예외발생 당시의 호출스택(call stack)에 있었던 **메서드의 정보와 예외 메시지**를 화면에 출력한다.
- `getMessage()` : 발생한 예외클래스의 인스턴스에 저장된 **메시지**를 얻을 수 있다.

위 소스 출력 예시

```
1
java.lang.ArithmeticException: / by zero
	at ExceptionEx8.main(ExceptionEx8.java:7)
예외메시지 : / by zero
```

#### 멀티 catch블럭

`|` 기호로 여러 catch블럭을 합칠 수 있게 한다.

```java
try {
  // ...
} catch (ExceptionA | ExceptionB e) {
  e.printStackTrace();
}
```

- 멀티 catch블럭으로 연결된 예외 클래스가 조상과 자손 관계에 있으면 컴파일 에러가 발생한다.

  - 불필요한 코드는 제거하라는 의미에서 에러가 발생하는 것 (조상클래스만 써줄 것)

- 멀티 catch블럭 내에서는 실제로 어떤 예외가 발생한 것인지 알 수 없다. 

  ```java
  try {
    // ...
  } catch (ExceptionA | ExceptionB e) {
    e.methodA();			// 에러. ExceptionA에 선언된 methodA()는 호출 불가
    if (e instanceof ExceptionA) {
      ExceptionA e1 = (ExceptionA)e;
      e1.methodA();		// OK. ExceptionA에 선언된 메서드 호출 가능
    }
  }
  ```

  - 그래서 예외 클래스들의 공통 분모인 조상 예외 클래스에 선언된 멤버만 사용할 수 있다.
  - 필요하다면 `instanceof`로 어떤 예외가 발생한 것인지 확인하고 개별적으로 처리할 수 있다. (이렇게까지 해가면서 블럭을 합치는 일은 거의 없을 것이다.)

- 멀티 catch블럭에 선언된 참조변수 e는 상수이므로 값을 변경할 수 없다.

  - 여러 catch블럭이 하나의 참조변수를 공유하기 때문에 생기는 제약이다. (실제로 참조변수의 값을 바꿀 일은 없을 것이다.)




### 출처📎

- 자바의 정석