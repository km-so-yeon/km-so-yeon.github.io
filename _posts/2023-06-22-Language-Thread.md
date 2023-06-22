---
title: "[Java] Thread 스레드"
author: "김소연"
date: 2023-06-22 18:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## Thread 스레드

java 명령어를 사용해서 클래스를 실행시키는 순간 자바 프로세스가 시작되고, main 메소드가 수행되면서 하나의 쓰레드가 시작된다. 
많은 쓰레드가 필요하다면, main 메소드에서 쓰레드를 생성해주면 된다. 
(하나의 프로세스 내에 여러 쓰레드가 수행된다.)

- 웹에서는 WAS를 사용하고, 똑같이 main 메소드에서 생성한 쓰레드들이 수행되는 것이다.
- 아무런 쓰레드를 생성하지 않아도 JVM을 관리하기 위한 여러 쓰레드가 존재한다.



## 쓰레드 등장 배경

프로세스가 하나 시작하려면 많은 자원이 필요하다.

- 하나의 작업을 수행하려고 할 때 여러 프로세스를 띄워서 실행하면 각각 메모리를 할당해주어야 한다.
  - OS마다 다르지만 프로세스는 적어도 32MB~64MB의 물리 메모리를 점유하고, 
    쓰레드는 1MB를 점유한다. (쓰레드를 경량 프로세스라고도 한다.)
- 요즘 멀티 코어 시대이기 때문에 다중 쓰레드로 실행하는 것이 더 빠른 시간 내에 결과를 가져다준다.



## 쓰레드를 생성하는 방법

1. Runnable 인터페이스 구현

2. Thread 클래스 사용

위와 같이 크게 두 가지가 있다. 모두 java.lang 패키지 아래에 있어서 별도로 import할 필요가 없다.



### 1. Thread 수행 내용 구현

**run() 메소드**를 구현한다.

#### Runnable 인터페이스 구현

```java
public class RunnableSample implements Runnable {
	public void run() {
		// 새로운 스레드로 수행되는 내용
	}
}
```

구현한 run() 메소드 내 문장들을 실행하고 끝난다.

#### Thread 클래스 확장

```java
public class ThreadSample extends Thread {
	public void run() {
		// 새로운 스레드로 수행되는 내용
	}
}
```

위와 같이 run() 메소드를 구현한다.



### 2. Thread 시작

```java
public class RunThread {
	public static void main(String[] args) {
		ThreadSample t = new ThreadSample();
		t.runBasic();
	}
	public void runBasic() {
		// 1. Runnable 을 구현한 클래스 실행
        RunnableSample runnable = new RannableSample();
        new Thread(runnable).start();

		// 2. Thread 를 확장한 클래스 실행
		ThreadSample thread = new ThreadSample();
        thread.start();
	}
}
```

- Runnable을 구현한 클래스 실행 시
  - Thread 클래스의 생성자에 해당 객체를 추가해서 시작해야 한다.
- Thread를 확장한 클래스 실행 시
  - 해당 객체에 바로 start() 메소드를 호출한다.



#### 두 가지 방법을 제공하는 이유

자바에서는 단일상속만 가능한데 이미 상속받는 클래스가 있는 경우 다중상속이 불가능하므로 Thread를 상속받을 수 없어서 해당 클래스를 쓰레드로 만들 수 없다.

하지만 인터페이스는 여러 개를 구현해도 문제가 없기 때문에, 
**쓰레드 클래스가 다른 클래스를 확장할 필요가 있는 경우 Runnable 인터페이스를 구현해서 사용하면 된다.**



#### Thread의 비동기

기존의 자바 프로그램은 순차적으로 실행했다. 한 줄의 코드가 실행이 끝날 때까지 기다렸다가 다음 줄이 실행된다.

하지만 **Thread 클래스의 start() 메소드는 끝날 때까지 기다려주지 않고 다음 줄을 실행한다.**
쓰레드 클래스에 있는 run() 메소드의 내용이 끝나든, 끝나지 않든 신경쓰지 않는다. 그렇기 때문에 어떤 쓰레드가 먼저 종료될지 알 수 없다.

새로 생성된 쓰레드들은 run() 메소드가 종료되면 끝난다. (예외로 데몬 쓰레드라는 것이 있다.)



### Thread 클래스의 생성자

| 생성자                               | 설명                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| Thread()                             | 새로운 쓰레드를 생성한다.                                    |
| Thread(Runnable target)              | 매개 변수로 받은 target 객체의 run() 메소드를 수행하는 쓰레드를 생성한다. |
| Thread(Runnable target, String name) | 매개 변수로 받은 target 객체의 run() 메소드를 수행하고, name이라는 이름을 갖는 쓰레드를 생성한다. |
| Thread(String name)                  | name이라는 이름을 갖는 쓰레드를 생성한다.                    |

- 모든 쓰레드는 이름이 있다.
  - 쓰레드에 아무런 이름을 지정하지 않으면, "Thread-n"으로 지정된다. (n은 쓰레드가 지정된 순서에 따라 증가)
  - 쓰레드 이름이 겹쳐도 예외나 에러가 발생하지는 않는다.
- ThreadGroup 클래스
  - 쓰레드를 생성할 때 쓰레드를 묶어놓을 수 있다.
  - 클래스에서 제공하는 여러 메소드들을 통해 각종 정보를 얻을 수 있다.
- stackSize
  - 자바 프로세스 실행 시 Runtime Data Area가 구성되는데, 그 중 하나이다.
  - 쓰레드가 생성될 때마다 별도의 스택이 생성된다.
  - 쓰레드에서 얼마나 많은 메소드를 호출하는지, 얼마나 많은 쓰레드가 동시에 처리되는지 JVM이 실행되는 OS 플랫폼에 따라 다르다. 
  - 경우에 따라 이 값이 무시될 수 있다.



#### Thread를 시작할 때 값을 전달하고 싶은 경우

1. 클래스 변수를 선언하고 
2. 생성자에 매개변수로 넘겨줘서 클래스 변수를 설정한다.

```java
public class ThreadObject extends Thread {
    private int calcNumber;
    public ThreadObject(String name, int calcNunber) {
        super(name);	// Thread 이름 설정
        this.calcNumber = calcNumber;	// 값 전달
    }
    public void run() {
        calcNumber++;
    }
}
```



## sleep() 메소드

| 메소드                                    | 설명                                                         |
| ----------------------------------------- | ------------------------------------------------------------ |
| static void sleep(long millis)            | 매개 변수로 넘어온 시간(1/1,000초) 만큼 대기한다.            |
| static void sleep(long millis, int nanos) | 첫 번째 매개변수로 넘어온 시간(1/1,000초) + 두 번째 매개변수로 넘어온 시간(1/1,000,000,000초)만큼 대기한다. |

- 밀리초 단위로 단위로 멈춘다.

- static하게 호출한다. `Thread.sleep(1000)`
- try-catch로 묶어주어야 한다.
  - InterruptedException 을 던질 수도 있다고 선언되어있기 때문이다.
    (InterruptedException이나 혹은 그 상위에 있는 예외로 catch해주어야 한다.)



## 속성을 확인하고 지정하는 메소드

| 메소드                                  | 설명                                                      |
| --------------------------------------- | --------------------------------------------------------- |
| void **run**()                          | 구현해야하는 메소드                                       |
| long **getId**()                        | 쓰레드의 고유 ID를 리턴한다. JVM에서 자동으로 생성해준다. |
| String **getName**()                    | 쓰레드의 이름을 리턴한다.                                 |
| void **setName**(String name)           | 쓰레드의 이름을 지정한다.                                 |
| int **getPriority**()                   | 쓰레드의 우선 순위를 확인한다.                            |
| void **setPriority**(int newPriority)   | 쓰레드의 우선 순위를 지정한다.                            |
| boolean **isDaemon**()                  | 쓰레드가 데몬인지 확인한다.                               |
| void **setDaemon**(boolean on)          | 쓰레드를 데몬으로 설정할지 아닌지 설정한다.               |
| StackTraceElement[] **getStackTrace**() | 쓰레드의 스택 정보를 확인한다.                            |
| Thread.State **getState**()             | 쓰레드의 상태를 확인한다.                                 |
| ThreadGroup **getThreadGroup**()        | 쓰레드의 그룹을 확인한다.                                 |

#### 쓰레드의 우선 순위 Priority

대기하고 있는 상황에서 더 먼저 수행할 수 있는 순위

- 대부분 이 값은 기본값으로 사용하는 것을 권장한다. (마음대로 정했다가 장애로 연결될 수 있음)
- 정할 일이 있으면 숫자로 정하는 것보다 API 내에 있는 상수를 이용할 것을 권장한다.
  - MAX_PRIORITY : 가장 높은 우선 순위 (값 == 10)
  - NORM_PRIORITY : 일반 쓰레드의 우선 순위 (값 == 5)
  - MIN_PRIORITY : 가장 낮은 우선 순위 (값 == 1)

#### 데몬 쓰레드

```java
ThreadSample thread1 = new ThreadSample();
ThreadSample thread2 = new ThreadSample();
ThreadSample daemonThread = new ThreadSample();

daemonThread.setDaemon(true);	// 데몬 쓰레드로 지정
System.out.println(thread1.isDaemon());	// false
System.out.println(daemonThread.isDaemon());	// true
```

사용자 쓰레드는 JVM이 해당 쓰레드가 끝날 때까지 기다린다.

즉, 어떤 쓰레드를 데몬 쓰레드로 지정하면, 그 쓰레드가 수행되고있든 아니든 JVM이 끝날 수 있다.

단, 해당 쓰레드가 시작되기(start() 메소드 호출하기) 전에 데몬 쓰레드로 지정되어야 한다.

- 만들어진 이유 : 부가적인 작업을 수행하는 쓰레드를 선언할 때 사용하기 위해
  - 예 : 모니터링하는 쓰레드를 별도로 띄워서 모니터링하다가, 주요 쓰레드가 종료되면 관련된 모니터링 쓰레드가 종료되어야 프로세스가 종료될 수 있다. 이 때 모니터링 쓰레드를 데몬 쓰레드로 만들어 프로세스가 종료될 수 있게 한다.



## Synchronized





## 출처

자바의 신
