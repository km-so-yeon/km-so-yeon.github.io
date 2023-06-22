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



### 1. run() 함수 구현

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



### 2. 클래스 실행

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

