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




## 예외 발생시키기

#### 방법

1. 먼저, 연산자 new를 이용해서 발생시키려는 예외 클래스의 객체를 만든 다음
2. 키워드 `throw`를 이용해서 예외를 발생시킨다.

```java
class ExceptionEx {
  public static void main(String args[]) {
    try {
      Exception e = new Exception("고의로 발생시킴");
      throw e;		// 예외를 발생시킴
      //throw new Exception("고의로 발생시킴");	// 위의 두줄을 한줄로 줄일 수 있다.
    } catch (Exception e) {
      e.printStackTrace();
    }
      System.out.println("정상 종료");
  }
}
```

실행 결과

```
java.lang.Exception: 고의로 발생시킴
	at ExceptionEx.main(ExceptionEx.java:4)
정상 종료
```

#### checked 예외, unchecked 예외

checked 예외

- Exception 클래스들이 발생할 가능성이 있는 문장에 예외처리를 하지 않으면 컴파일 되지 않는다.
- 예외처리를 확인하는 Exception 클래스들은 checked예외라고 한다.

unchecked 예외

- RuntimeException 클래스들이 발생할 가능성이 있는 문장에 예외처리를 하지 않으면 컴파일은 성공하지만, 비정상적으로 종료된다.
- 예외처리를 확인하지 않는 RuntimeException 클래스들은 unchecked예외라고 한다.



## 메서드에 예외 선언하기

#### 방법

1. 메서드 선언부에 키워드 `throws`를 사용해서 메서드 내에서 발생할 수 있는 예외를 적어준다.
2. 예외가 여러 개일 경우 쉼표`,`로 구분한다.

```java
void method() throws Exception1, Exception2, ... ExceptionN {
  // 메서드 내용
}
```

- 적어준 예외 뿐만 아니라 그 자손타입의 예외까지도 발생할 수 있다.

- 메서드 선언부에 발생 가능성 있는 예외를 명시하여 이 메서드를 사용하는 쪽에서 이에 대한 처리를 강요하도록 한다.

  - 사용하는 쪽에서 예외처리를 하지 않고 종료된다면 예외로 인해 비정상적으로 종료된다.
  - 이를 통해 프로그래머들의 짐을 덜어주고, 견고한 프로그램 코드를 작성할 수 있도록 도와준다.

- 예외가 발생한 메서드 내에서 자체적으로 처리해도 되는 것은 메서드 내에서 try-catch문을 사용해서 처리하고, 메서드 내에서 자체적으로 해결이 안되는 경우에는 예외를 메서드에 선언해서 호출한 메서드에서 처리해야한다.

  - 자체적으로 처리

    ```java
    class ExceptionEx {
      public static void main(String[] args) {
        method1();
      }
      
      static void method1() {
        try {
          throw new Exception();
        } catch (Exception e) {
          System.out.prinln("method1에서 예외처리");
          e.printStackTrace();
        }
      }
    }
    ```

  - 호출한 메서드에서 처리

    ```java
    class ExceptionEx {
      public static void main(String[] args) {
        try {
          method1();
        } catch (Exception e) {
          System.out.prinln("main에서 예외처리");
          e.printStackTrace();
        }
      }
      
      static void method1() throws Exception{
        throw new Exception();
      }
    }
    ```

    ​


## finally 블럭

- 목적 : 예외의 발생여부에 상관없이 실행되어야할 코드를 포함시키기 위해 사용한다.
- 사용법 : try-catch문 끝에 선택적으로 덧붙인다.
- 실행 순서 : try → catch → finally (예외가 발생하지 않은 경우 catch 생략)

```java
try {
  // 예외가 발생할 가능성이 있는 문장들을 넣는다.
} catch (Exception1 e) {
  // 예외처리를 위한 문장을 적는다.
} finally {
  // 예외의 발생여부에 관계없이 항상 수행되어야하는 문장들을 넣는다.
}
```

- try, catch블럭 내에 return문이 있더라도 finally블럭 내 문장들을 실행하고 종료한다.



## 자동 자원 반환 (try-with-resources문)

- JDK1.7부터 사용 가능

- 주로 입출력 관련 클래스에서 사용

  - 입출력에 사용되는 클래스는 사용 후 자원(resources)를 반환하기 위해 닫아줘야한다.

    ```java
    try {
      fis = new FileInputStream("score.dat");
      dis = new DataInputStream(fis);
    } catch (IOException ie) {
      ie.printStackTrace()
    } finally {
      try {		// close()에서 발생할 수 있는 예외를 처리하기 위해
        if(dis != null) 
        	dis.close();
      } catch (IOException ie) {
        ie.printStackTrace()
      }
    }
    ```

    1. 코드가 복잡해졌다.
    2. try블럭과 finally블럭에서 모두 예외가 발생하면, try블럭의 예외는 무시된다.

    이를 개선하기 위해 try-with-resources문이 되었다.



#### 사용방법

```java
try (FileInputStream fis = new FileInputStream("score.dat");
	DataInputStream dis = new DataInputStream()) {
  // 코드 작성..
} catch (IOException ie) {
  ie.printStackTrace();
}
```

- 괄호`()` 안에 객체를 생성하는 문장을 넣으면, try블럭을 벗어나는 순간 자동적으로 close()가 호출된다.
  - 생성된 객체는 try블럭 내에서만 사용 가능하다.
  - 자동으로 close()가 호출될 수 있으려면 클래스가 `AutoCloseable`이라는 인터페이스를 구현한 것이어야 한다.
- 괄호`()`안에 두 문장 이상 넣을 경우 `;`으로 구분한다.



#### 억제된 예외 (suppressed)

try-with-resources문을 사용하면
try-catch만 사용했을 때 try블럭과 catch블럭에서 모두 예외가 발생할 경우 **try블럭에서 발생해서 무시되었을 예외**를 출력할 수 있다.

이 때 출력된 예외를 `억제된 예외`라고 한다.

억제된 예외에 대한 정보는 실제 발생한 예외에 저장된다.

```java
class TryWithResourcesEx {
  public static void main(String[] args) {
    try (CloseableResource cr = new CloseableResource) {
      cr.exceptionWork(false);	// 예외가 발생하지 않는다.
    } catch (WorkException e) {
      e.printStackTrace();
    } catch (CloseEXception e) {
      e.printStackTrace();
    }
    
    try (CloseableResource cr = new CloseableResource) {
      cr.exceptionWork(true);	// 예외가 발생한다.
    } catch (WorkException e) {
      e.printStackTrace();
    } catch (CloseEXception e) {
      e.printStackTrace();
    }
  }
}

class CloseableResource implements AutoCloseable {
  public void exceptionWork(boolean exception) throws WorkException {
    System.out.println("exceptionWork(" + exception + ")가 호출됨");
    
    if(exception)
    	throw new WorkException("WorkException 발생");
  }
  
  public void close() throws CloseException {
    System.out.println("close() 호출됨");
    throw new CloseException("CloseException 발생");
  }
}

class WorkException extends Exception {
  WorkException(String msg) { super(msg); }
}

class CloseException extends Exception {
  CloseException(String msg) { super(msg); }
}
```

실행결과

```
exceptionWork(false)가 호출됨
close() 호출됨
CloseException: CloseException 발생
	at CloseableResource.close(TryWithResourcesEx.java:33)
	at TryWithResourcesEx.main(TryWithResourcesEx.java:6)
	
exceptionWork(true)가 호출됨
close() 호출됨
WorkException: WorkException 발생
	at CloseableResource.exceptionWork(TryWithResourcesEx.java:28)
	at TryWithResourcesEx.main(TryWithResourcesEx.java:14)
	CloseException: CloseException 발생
		at CloseableResource.close(TryWithResourcesEx.java:33)
		at TryWithResourcesEx.main(TryWithResourcesEx.java:15)
```

- 두 예외가 동시에 발생할 수는 없기 때문에 
  실제 발생된 예외는 WorkException, 억제된 예외는 CloseException으로 다룬다.



#### Throwable에 정의된 억제된 예외와 관련된 메서드

```
void addSuppressed(Throwable exception)		억제된 예외를 추가
Throwable[] getSuppressed()					억제된 예외(배열)을 반환
```



## 사용자정의 예외

- 필요에 따라 프로그래머가 새로운 예외 클래스를 정의하여 사용할 수 있다.
- 가능하면 새로운 예외클래스를 만들기보다 기존의 예외클래스를 사용하자.



#### 방법

`Exception`클래스 또는 `RuntimeException`으로부터 상속받아 클래스를 만든다.

```java
class MyException extends Exception {
  // 에러코드 값을 저장하기 위한 필드를 추가했다.
  pricate final int ERR_CODE;	// 생성자를 통해 초기화한다.
  
  MyException(String msg, int errCode) {	// 생성자
    super(msg);		// 조상인 Exception클래스의 생성자를 호출한다.
    ERR_CODE = errCode;
  }
  
  MyException(String msg){	// 생성자
    this(msg, 100);	// ERR_CODE를 100(기본값)으로 초기화한다.
  }
  
  public int getErrCode(){	// 에러 코드를 얻을 수 있는 메서드도 추가한다.
    return ERR_CODE;		// 이 메서드는 주로 getMessage()와 함께 사용될 것이다.
  }
}
```



## 예외 되던지기 (exception re-throwing)

예외를 처리한 후 인위적으로 다시 발생시키는 것

하나의 예외에 대해서 예외가 발생한 메서드와 이를 호출한 메서드 양쪽 모두에서 처리해줘야 할 작업이 있을 때 사용된다.

#### 방법

1. 예외가 발생할 가능성이 있는 메서드에서 try-catch문을 사용해서 예외 처리하기
2. catch문에서 필요한 작업을 수행한 후 throw문을 사용해서 예외를 다시 발생시키기
   (+메서드의 선언부에 발생할 예외를 throws에 지정해주어야 한다.)
3. 다시 발생한 예외는 이 메서드를 호출한 메서드에게 전달되어 호출한 메서드의 try-catch문에서 예외를 또다시 처리

```java
class RethrowingEx {
  public static void main(String[] args) {
    try {
      method1();
    } catch (Exception e) {
      System.out.println("main메서드에서 예외가 처리되었습니다.");
    }
  }
  
  public void method1() throws Exception {
    try {
      throw new Exception();
    } catch (Exception e) {
      System.out.println("method1메서드에서 예외가 처리되었습니다.");
      throw e;	// 다시 예외를 발생시킨다.
    }
  }
}
```

실행결과

```
method1메서드에서 예외가 처리되었습니다.
main메서드에서 예외가 처리되었습니다.
```

- 반환값이 있는 메서드일 경우

  ```java
  static int method() {
    try {
      return 0;
    } catch (Exception e) {
      // return 1;
      throw new Exception();
    } finally {
      System.out.println("method1메서드의 finally블럭이 수행되었습니다.");
    }
  }
  ```

  - catch블럭에서도 return문이 있어야 한다.
    - 또는 예외 되던지기를 해서 호출한 메서드로 예외를 전달하면 return문이 없어도 된다.
  - finally블럭에서도 return문을 사용할 수 있으며, try블럭이나 catch블럭 return문 다음에 수행된다.
    최종적으로 finally블럭 내의 return문 값이 반환된다.



## 연결된 예외(chained exception)

예외 A가 예외 B를 발생시켰다면, A를 B의 원인 예외(cause exception)이라고 한다.

```
Throwable initCause(Throwable cause)	지정한 예외를 원인 예외로 등록
Throwable getCause()					원인 예외를 반환
```



SpaceException을 원인 예외로 하는 InstallException을 발생시키는 방법

```java
try {
  startInstall();	// SpaceException 발생
  copyFiles();
} catch (SpaceException e) {
  InstallException ie = new InstallException("설치 중 예외 발생");	// 예외 생성
  ie.initCause(e);	// InstallException의 원인 예외를 SpaceException으로 지정
  throw ie;			// InstallException을 발생시킨다.
} catch (MemoryException me) {
  // ...
}
```



#### 발생한 예외를 원인 예외로 등록해서 다시 예외를 발생시키는 이유

- **여러가지 예외를 하나의 큰 분류의 예외로 묶어서 다루기 위해서**

  - InstallException을 SpaceException과 MemeryException의 조상으로 해서 catch블럭을 작성하면,

    ```java
    try {
      startInstall();	// SpaceException 발생
      copyFiles();
    } catch (InstallException e) {
      e.printStackTrace();
    }
    ```

    - 실제 발생한 예외가 알 수 없다는 문제가 생긴다. 
    - SpaceException과 MemoryException의 상속관계를 변경해야되는 것이 부담이 된다.

    → 예외가 원인 예외를 포함할 수 있게 하면 두 예외는 상속관계가 아니어도 상관없다.

- **checked예외를 unchecked예외로 바꿀 수 있도록 하기 위해서**

  - checked예외를 unchecked예외로 바꾸면 예외처리가 선택적이 되므로 억지로 예외처리를 하지 않아도 된다.

    ```java
    static void startInstall() throws SpaceException {
      if(!enoughSpace()) {
        throw new SpaceException("설치할 공간이 부족합니다.");
      }
      
      if(!enoughMemory()) {
        throw new RuntimeException(new MemoryException("메모리가 부족합니다."));
      }
    }
    ```

    - MemoryException은 Exception의 자손이어서 반드시 예외처리를 해야되는데,
      이 예외를 RuntimeException으로 감싸서 unchecked예외로 바꿨다.

      - 그래서 throws에 MemoryException을 선언하지 않아도 된다.

      - initCause() 대신 RuntimeException 생성자 사용

        ```
        RuntimeException(Throwable cause)		원인 예외를 등록하는 생성자
        ```

        ​

### 출처📎

- 자바의 정석