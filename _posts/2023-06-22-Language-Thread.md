---
title: "[Java] Thread 스레드"
author: "김소연"
date: 2023-06-22 18:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## Thread 스레드

java 명령어를 사용해서 클래스를 실행시키는 순간 자바 프로세스가 시작되고, 
main 메소드가 수행되면서 하나의 쓰레드가 시작된다. 

많은 쓰레드가 필요하다면, main 메소드에서 쓰레드를 생성해주면 된다. 
(하나의 프로세스 내에 여러 쓰레드가 수행된다.)

- 웹에서는 WAS를 사용하고, 똑같이 main 메소드에서 생성한 쓰레드들이 수행되는 것이다.
- 아무런 쓰레드를 생성하지 않아도 JVM을 관리하기 위한 여러 쓰레드가 존재한다.



#### 프로세스와 스레드 차이

| 프로세스                                                     | 스레드                                                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 실행되고 있는 프로그램을 의미한다.<br />- 각각 **독립된 메모리 영역**(Code, Data, Heap, Stack)을 할당받는다.<br />- 한 프로세스가 다른 프로세스의 자원에 접근하려면 프로세스 간의 통신을 사용해야 한다.<br />- 최소 1개의 스레드(메인 스레드)를 가지고 있다. | 프로세스 내에서 실행되는 여러 흐름의 단위를 의미한다.<br />- 프로세스 내에서 각각 **Stack만 따로 할당**받고 Code, Data, Heap 영역은 공유한다. |

- 자바에는 프로세스가 존재하지 않고 스레드만 존재하며, 자바 스레드는 JVM에 의해 스케쥴되는 실행 단위 코드 블록이다.



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
  - `InterruptedException` 을 던질 수도 있다고 선언되어있기 때문이다.
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

즉, 어떤 쓰레드를 **데몬 쓰레드로 지정하면, 그 쓰레드가 수행되고있든 아니든 JVM이 끝날 수 있다.**

단, 해당 쓰레드가 시작되기(start() 메소드 호출하기) 전에 데몬 쓰레드로 지정되어야 한다.

- 만들어진 이유 : 부가적인 작업을 수행하는 쓰레드를 선언할 때 사용하기 위해
  - 예 : 모니터링하는 쓰레드를 별도로 띄워서 모니터링하다가, 주요 쓰레드가 종료되면 관련된 모니터링 쓰레드가 종료되어야 프로세스가 종료될 수 있다. 이 때 모니터링 쓰레드를 데몬 쓰레드로 만들어 프로세스가 종료될 수 있게 한다.



## Synchronized

어떤 클래스나 메소드가 스레드에 안전하게 하기 위해 사용하는 예약어
- 여러 쓰레드가 한 객체에 선언된 메소드에 접근하여 데이터를 처리하려고 할 때 동시에 연산을 수행하여 값이 꼬이는 경우가 발생할 수 있다. (메소드에서 인스턴스 변수를 수정하려고 할 때) 이러한 문제를 해결하기 위해서 생겨났다.



### 사용 방법

- 메소드 자체를 synchronized로 선언하는 방법
- 메소드 내의 특정 문장만 synchronized로 감싸는 방법



**메소드 자체를 synchronized로 선언하는 방법**

```java
public synchronized void plus(int value) {
	amount += value;
}
```

동일한 객체의 메소드에 여러 개의 쓰레드가 접근하더라도 한 순간에는 하나의 쓰레드만 이 메소드를 수행하게 된다.




**메소드 내의 특정 문장만 synchronized로 감싸는 방법**

위 방법에서 성능 상 문제가 발생할 수 있다. 긴 메소드 중 일부에서만 인스턴스 변수를 다룬다면 나머지에서는 필요없는 대기 시간이 생기게 된다. 

이 때 일부만 synchronized 처리를 해주면 된다.

```java
public void plus(int value) {
	synchronized(this) {
		amount += value
	}
}
```

중괄호 내에 있는 연산만 여러 쓰레드에서 처리하지 않겠다는 의미이다.

- 소괄호 안에 있는 this는 잠금 처리를 하기 위한 객체를 선언한다. 하나의 쓰레드만 수행할 수 있도록 허용하고 블록에 들어간 쓰레드가 다 처리하고 나오면 대기하고있는 다음 쓰레드에게 기회를 준다.

```java
private int amount;
private int interest;
private Object interestLock = new Object();
private Object amountLock = new Object();

public void addInterest(int value) {
    synchronized(interestLock) {
        interest += value;
    }
}
public void plus(int value) {
    synchronized(amountLock) {
        amount += value;
    }
}
```

- 일반적으로는 이처럼 별도의 객체를 선언하여 사용한다.
    - 여러 개의 synchronized 블록에 같은 객체를 사용하면 두 블록 모두 하나의 쓰레드에서만 접근 가능하게된다. 그러므로 각각의 블록에 별도의 객체를 사용하면 각 블록 당 하나씩 쓰레드가 접근할 수 있게 된다.

```java
public class CommonCalculate {
	private int amount;
	public synchronized void plus(int value) {
		amount += value;
	}
}

public class ModifyAmountThread extends Thread {
	private CommonCalculate calc;
	public ModifyAmountThread(CommonCalculate calc) {
		this.calc = calc;
	}
	public void run() {
		for(int loop = 0; loop < 10000; loop++) {
			calc.plus(1);
		}
	}
}
```

```java
CommontCalculate calc1 = new CommontCalculate();
CommontCalculate calc2 = new CommontCalculate();

ModifyAmountThread thread1 = new ModifiyAmountThread(calc1);
ModifyAmountThread thread2 = new ModifiyAmountThread(calc1);	// synchronized 유효
ModifyAmountThread thread3 = new ModifiyAmountThread(calc2);
```

- 메소드를 synchronized할 때는 같은 객체를 참조할 때만 유효하다. (thread1, thread2)
    - 서로 다른 객체를 참조한다면 synchronized로 선언된 메소드는 같은 객체를 참조하는 것이 아니므로, synchronized를 안쓰는 것과 동일하다고 보면 된다.



## 쓰레드를 통제하는 메소드

| 메소드                                | 설명                                                         |
| ------------------------------------- | ------------------------------------------------------------ |
| Thread.state **getState**()           | 쓰레드의 상태를 확인한다.                                    |
| void **join**()                       | 수행 중인 쓰레드가 중지할 때까지 대기한다.                   |
| void **join**(long millis)            | 매개변수에 지정된 시간만큼(1/1,000초) 대기한다.              |
| void **join**(long millis, int nanos) | 첫 번째 매개변수로 넘어온 시간(1/1,000초) + 두 번째 매개변수로 넘어온 시간(1/1,000,000,000초)만큼 대기한다. |
| void **interrupt**()                  | 수행중인 쓰레드에 중지 요청을 한다.                          |



#### 쓰레드의 상태 Thread.state

Thread 클래스에 State라는 enum 클래스가 있다.
이 클래스에 선언되어 있는 상수들의 목록이다.

| 상태          | 의미                                                         |
| ------------- | ------------------------------------------------------------ |
| NEW           | 쓰레드 객체는 생성되었지만, 아직 시작하지는 않은 상태        |
| RUNNABLE      | 쓰레드가 실행 중인 상태                                      |
| BLOCKED       | 쓰레드가 실행 중지 상태이며, 모니터 락이 풀리기를 기다리는 상태 |
| WAITING       | 쓰레드가 대기중인 상태                                       |
| TIMED_WAITING | 특정 시간만큼 쓰레드가 대기 중인 상태                        |
| TERMINATED    | 쓰레드가 종료된 상태                                         |

쓰레드는 `NEW → 상태 → TERMINATED` 라는 라이프 사이클을 가진다.



#### **join**(long millis, int nanos)

첫 번째 매개변수가 음수이거나, 두 번째 매개변수가 0 ~ 999,999 사이의 값이 아니라면 `IllegalArgumentException` 이라는 예외가 발생하기 된다.



#### **interrupt**()

현재 수행중인 쓰레드를 `InterruptedException`을 발생시키면서 중단시킨다.

- sleep()과 join() 메소드에서 발생한다고 했던 예외이다.
  즉, sleep()과 join() 같이 대기 상태를 만든다는 메소드가 호출되었을 때 interrupt() 메소드를 호출할 수 있다.
- 쓰레드가 시작하기 전이나, 종료된 상태에서 interrupt()를 호출하면 예외나 에러 없이 다음 문장으로 넘어간다.



```java
public class SleepThread extends Thread {
    long sleepTime;
    public SleepThread(long sleepTime) {
        this.sleepTime = sleepTime;
    }
    public void run() {
        try {
            Thread.sleep(sleepTime);
        } catch(InterruptedException ie) {
            ie.printStackTrace();
        }
    }
}
```

```java
SleepThread thread = new SleepThread(2000);
try {
    thread.start();
    thread.join(500);		// 0.5초간 대기
    thread.interrupt();		// InterruptedException 발생
} catch (InterruptedException ie) {
    ie.printStackTrace();
}
```

SleepThread에 2000을 넣고 생성했기 때문에 2초간 대기하는데
join() 메소드에서 0.5초간 대기하고 interrupt()가 실행되기 때문에

쓰레드가 종료되지 않은 상태에서 interrupt()가 실행되어 InterruptedException이 발생한다.

만약 join() 메소드에서 2000이 넘는 값을 주었을 경우 InterruptedException이 발생하지 않는다.



## 쓰레드의 상태를 확인하는 메소드

| 메소드                           | 설명                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| void **checkAccess**()           | 현재 수행중인 쓰레드가 해당 쓰레드를 수정할 수 있는 권한이 있는지 확인한다. 만약 권한이 없다면 SecurityException을 발생시킨다. |
| boolean **isAlive**()            | 쓰레드가 살아있는지 확인한다. 해당 쓰레드의 run() 메소드가 종료되었는지 아닌지 확인한다. |
| boolean **isInterrupted**()      | run() 메소드가 정상적으로 종료되지 않고, interrupt() 메소드를 통해 종료되었는지 확인하는데 사용한다. |
| static boolean **interrupted**() | 현재 쓰레드가 중지되었는지 확인한다.                         |



## Object 클래스에 선언된 쓰레드와 관련있는 메소드들

| 메소드                                 | 설명                                                         |
| -------------------------------------- | ------------------------------------------------------------ |
| void **wait**()                        | 다른 쓰레드가 Object 객체에 대한 notify() 메소드나 notifyAll() 메소드를 호출할 때까지 현재 쓰레드가 대기하고 있도록 한다. |
| void **wait**(long timeout)            | wait() 메소드와 동일한 기능을 제공하며, 매개변수에 지정한 시간만큼만 대기한다.<br />즉, 매개변수 시간을 넘어섰을 때에는 현재 쓰레드는 다시 깨어난다. 시간은 밀리초로 1/1,000초 단위이다. |
| void **wait**(long timeout, int nanos) | wait() 메소드와 동일한 기능을 제공한다. 이 메소드는 밀리초+나노초 만큼만 대기한다. 뒤에 있는 나노초는 0~999,999 사이의 값만 지정할 수 있다. |
| void **notify**()                      | Object 객체의 모니터에 대기하고 있는 단일 쓰레드를 깨운다.   |
| void **notifyAll**()                   | Object 객체의 모니터에 대기하고 있는 모든 쓰레드를 깨운다.   |

wait() 메소드를 사용하면 쓰레드가 대기 상태가 되고, notify()나 notifyAll() 메소드를 사용하면 쓰레드의 대기 상태가 해제된다.

- interrupt() 메소드를 호출하여 대기 상태에서 풀려날 수도 있지만 notiry() 메소드를 호출해서 풀어야 `InterruptedException`도 발생하지 않고, wait() 이후의 문장도 정상적으로 수행하게 된다.



## ThreadGroup 에서 제공하는 메소드들

### ThreadGroup

쓰레드의 관리를 용이하게 하기 위한 클래스이다.

기본적으로 운영체제의 폴더처럼 뻗어나가는 트리 구조를 가진다.

| 메소드                                                 | 설명                                                         |
| ------------------------------------------------------ | ------------------------------------------------------------ |
| int **activeCount**()                                  | 실행 중인 쓰레드의 개수를 리턴한다. (리턴값은 배열에 저장된 스레드의 개수) |
| int **activeGroupCount**()                             | 실행 중인 쓰레드 그룹의 개수를 리턴한다.                     |
| int **enumerate**(Thread[] list)                       | 현재 쓰레드 그룹에 있는 모든 쓰레드를 매개 변수로 넘어온 쓰레드 배열에 담는다. |
| int **enumerate**(Thread[] list, boolean recurse)      | 현재 쓰레드 그룹에 있는 모든 쓰레드를 매개 변수로 넘어온 쓰레드 배열에 담는다. 두 번째 매개 변수가 true이면 하위에 있는 쓰레드 그룹에 있는 쓰레드 목록도 포함한다. |
| int **enumerate**(ThreadGroup[] list)                  | 현재 쓰레드 그룹에 있는 모든 쓰레드 그룹을 매개 변수로 넘어온 쓰레드 그룹 배열에 담는다. |
| int **enumerate**(ThreadGroup[] list, boolean recurse) | 현재 쓰레드 그룹에 있는 모든 쓰레드 그룹을 매개 변수로 넘어온 쓰레드 그룹 배열에 담는다. 두 번째 매개 변수가 true이면 하위에 있는 쓰레드 그룹 목록도 포함한다. |
| String **getName**()                                   | 쓰레드 그룹의 이름을 리턴한다.                               |
| ThreadGroup **getParent**()                            | 부모 쓰레드 그룹을 리턴한다.                                 |
| void **list**()                                        | 쓰레드 그룹의 상세 정보를 출력한다.                          |
| void **setDaemon**(boolean daemon)                     | 지금 쓰레드 그룹에 속한 쓰레드들을 데몬으로 지정한다.        |

쓰레드 그룹에 있는 모든 쓰레드의 객체를 제대로 담으려면 

- activeCount() 메소드를 통해 현재 실행중인 쓰레드의 개수를 정확히 파악한 후
- 그 개수만큼의 배열을 생성하여 enumerate() 메소드로 배열에 담으면 된다.

```java
SleepThread sleep1 = new SleepThread(5000);
SleepThread sleep2 = new SleepThread(5000);

ThreadGroup group = new ThreadGroup("Group1");
Thread thread1 = new Thread(group, sleep1);
Thread thread2 = new Thread(group, sleep2);

thread1.start();
thread2.start();
Thread.sleep(1000);

int activeCount = group.activeCount();
Thread[] tempThreadList = new Thread[activeCount];
int result = group.enumerate(tempThreadList);
```





## 출처

- 자바의 신
- https://gmlwjd9405.github.io/2018/09/14/process-vs-thread.html
