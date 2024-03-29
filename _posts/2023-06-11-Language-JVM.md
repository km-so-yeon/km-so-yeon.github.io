---
title: "[Java] JVM"
author: "김소연"
date: 2023-06-11 15:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## JVM (Java Virtual Machine)

![jvm](/assets/img/jvm.png){:width="80%" height="80%"}

- 자바 가상 머신, 자바를 실행하기 위한 가상 컴퓨터
- 자바와 운영체제 사이에서 중개자 역할을 수행하고, 자바가 운영체제에 구애받지 않고 프로그램을 실행할 수 있도록 도와줍니다.

#### 특징

- 속도가 느립니다.
  - 일반 애플리케이션 코드는 OS만 거치고 하드웨어로 전달되는데, Java 애플리케이션은 JVM을 한번 더 거칩니다.
  - 하드웨어에 맞게 완전히 컴파일된 상태가 아니라 실행 시 해석(interpret)됩니다.
  - 요즘은 바이트코드(컴파일된 자바코드)를 하드웨어의 기계어로 바로 변환해주는 JIT 컴파일러와 향상된 최적화 기술이 적용되어 속도의 격차를 많이 줄였습니다.
- OS와 하드웨어에 독립적입니다.
  - Java 애플리케이션은 JVM하고만 상호작용을 하기 때문에 OS와 하드웨어에 독립적입니다.
  - JVM은 OS에 종속적이기 때문에 해당 OS에서 실행가능한 JVM이 필요합니다.
- 가비지 컬렉터를 사용하여 메모리 관리를 자동으로 수행합니다.
- 다른 하드웨어와 다르게 레지스터 기반이 아닌 스택 기반으로 동작합니다.



## 자바 프로그램 실행 단계

1. 자바로 개발된 프로그램을 실행하면 JVM이 OS로부터 메모리를 할당합니다.
2. 자바 컴파일러(javac)가 자바 소스코드(.java)를 자바 바이트코드(.class)로 컴파일합니다.
3. Class Loader는 동적 로딩을 통해 필요한 클래스들을 로딩 및 링크하여 Runtime Data Area로 올립니다.
4. Runtime Data Area에 로딩된 바이트코드들은 Execution Engine을 통해 해석합니다.
5. 해석된 바이트코드는 Runtime Data Area의 각 영역에 배치되어 수행하며 이 과정에서 Execution Engine에 의해 GC의 작동과 Thread 동기화가 이루어집니다.



## JVM 내부구조

![jvm2](/assets/img/jvm2.jpg){:width="80%" height="80%"}



### Class Loader

JVM 내로 클래스 파일을 동적으로 로드하고, 링크를 통해 배치하는 작업을 수행하는 모듈입니다.

즉, 바이트코드들을 엮어서 JVM의 메모리 영역인 Runtime Data Area에 배치합니다.
클래스를 한 번에 메모리에 올리지 않고, 애플리케이션에서 필요한 경우 동적으로 메모리에 적재합니다.

로딩 순서

1. Loading : 클래스 파일을 가져와서 JVM의 메모리에 로드한다.
2. Linking : 클래스 파일을 사용하기 위해 검증하는 과정이다.
3. Initialization : 클래스 변수들을 적절한 값으로 초기화한다.



### Execution Engine

Class Loader를 통해 JVM의 Runtime Data Area에 배치된 바이트 코드들을 명령어 단위로 읽어서 실행합니다.

바이트코드는 기계가 바로 수행할 수 있는 언어이기보다는 가상머신이 이해할 수 있는 중간 레벨로 컴파일 된 코드입니다. 그래서 바이트 코드를 실제로 JVM 내부에서 기계가 실행할 수 있는 형태로 변경해줍니다.

이 수행 과정에서 **인터프리터**와 **JIT 컴파일러** 두 가지 방식을 혼합하여 바이트코드를 실행합니다.

#### 인터프리터

바이트코드 명령어를 하나씩 읽어서 해석하고 바로 실행합니다. 
JVM 안에서 기본적으로 바이트코드는 인터프리터 방식으로 동작합니다. 하지만 같은 메소드여도 여러 번 호출이 된다면 매번 해석하고 수행해야해서 전체적인 속도는 느립니다.



#### JIT컴파일러

인터프리터의 단점을 보완하기 위해 도입된 방식입니다. 

반복되는 코드를 발견해서, 바이트 코드 전체를 컴파일하여 Native Code로 변경하고, 이후 해당 메서드를 더 이상 인터프리팅하지 않고 캐싱해두었다가 Native Code로 직접 실행하는 방식입니다.

하나씩 인터프리팅하는 것이 아니라, 컴파일된 네이티브 코드를 실행하는 것이기 때문에 전체적인 실행 속도는 인터프리팅 방식보다 빠릅니다.

- 장점 : 반복적으로 수행되는 코드는 매우 빠른 성능을 보입니다.
- 단점 : 처음에 시작할 때는 변환단계를 거쳐야하므로 성능이 느립니다.

바이트 코드를 기계코드로 바꾸면서 실행이 빨라졌지만 역시 변환하는 데 비용이 발생하였습니다. 이와 같은 이유로 JVM은 모든 코드를 JIT 컴파일러 방식으로 실행하지 않고, 인터프리터 방식을 사용하다가 일정한 기준이 넘어가면 JIT 컴파일러 방식으로 실행합니다.

(Native Code : C, C++, 어셈블리어로 구성된 코드)



### Garbage Collector (GC)

Runtime Data Area 중 Heap 메모리 영역에 생성된 객체들 중 참조되지 않는 객체들을 탐색 후 제거합니다.

GC가 역할을 하는 시간은 언제인지 정확히 알 수 없습니다. 특히 Full GC가 발생하는 경우, GC를 제외한 모든 스레드가 중지되기 때문에 장애가 발생할 수 있습니다.



### Runtime Data Area

JVM의 메모리 영역으로, 자바 애플리케이션을 실행할 때 사용되는 데이터들을 적재하는 영역입니다.

이 영역은 크게 Method Area, Heap Area, Stack Area, PC Register, Native Method Stack으로 나눌 수 있습니다.
이 때 Method Area, Heap Area는 모든 Thread가 공유하는 영역이고, 나머지는 각 Thread마다 생성되는 개별 영역입니다.

![jvm3](/assets/img/jvm3.png){:width="80%" height="80%"}



## Runtime Data Area

### Method Area

JVM이 시작될 때 생성되는 공간으로 바이트코드를 처음 메모리 공간에 올릴 때 초기화되는 대상을 저장하기 위한 메모리 공간입니다. 

- 초기화 코드 정보들
  - Filed Info : 멤버 변수의 이름, 데이터 타입, 접근 제어자 정보
  - Method Info : Method명, return 타입, 매개변수, 접근 제어자 정보
  - Type Info : Type명 (패키지명 + 클래스명), Class 인지 Interface 인지 여부(종류) 저장, Type의 속성(접근제어자 등), Super Class의 이름, 사용된 Interface 정보
  
  👉 JVM이 동작하고 클래스가 로드될 때 적재되서 프로그램이 종료될 때까지 저장됩니다.

- Runtime Constant Pool

  - 메서드 영역에 존재하는 별도의 관리 영역
  - 각 클래스/인터페이스마다 존재하고, 클래스를 생성할 때 참조해야할 정보들을 상수로 가지고 있는 영역입니다.
  - 이 Contrant Pool을 통해 해당 메소드나 필드의 실제 메모리 상 주소를 찾아 참조합니다.

  👉 상수 자료형을 저장하여 참조하고 중복을 막는 역할

- Class Variable
  - static 키워드로 선언된 변수가 저장된다.



### Heap Area

new 키워드로 생성된 클래스, 인스턴스 변수, 배열 타입 등 Reference Type이 저장되는 영역입니다. 

- Heap의 참조 주소는 Stack이 가지고 있고 해당 객체를 통해서 Heap 영역에 있는 인스턴스를 핸들링할 수 있습니다.

JVM이 관리하는 프로그램 상에서 데이터를 저장하기 위해 런타임 시 동적으로 할당하여 사용하고 Garbage Collector가 참조되지 않는 메모리를 확인하고 제거하는 영역입니다. 

- 효율적인 Garbage Collection을 수행하기 위해 5가지 영역으로 나뉘게 됩니다.
  - 5가지 영역은 물리적으로 Young Generation과 Old Generation 영역으로 구분됩니다.
- Young Generation : 생명 주기가 짧은 객체를 GC대상으로 하는 영역
  - Eden : new를 통해 새로 생성된 객체가 위치. 정기적인 GC후 살아남은 객체들은 Survivor로 이동
  - Survivor 0 / Survivor 1 : 각 영역이 채워지면, 살아남은 객체는 비워진 Survivor로 순차적으로 위치
- Old Generation : 생명 주기가 긴 객체를 GC대상으로 하는 영역. Young Generation에서 마지막까지 살아남은 객체가 위치

Method Area에 로드된 클래스만 생성이 가능합니다. 





### Stack Area

int, long, boolean 등 기본 자료형을 생성할 때 저장하는 영역이고, 임시적으로 사용되는 변수나 정보들이 저장되는 영역입니다.

![jvm4](/assets/img/jvm4.png){:width="80%" height="80%"}

메서드 호출 시마다 각각의 스택 프레임(각 메서드만을 위한 공간)을 생성합니다. 그리고 메서드 안에서 사용되는 값들을 저장하고, 호출된 메서드의 매개변수, 지역변수, 리턴값 및 연산 시 일어나는 값들을 임시로 저장합니다. 메서드 수행이 끝나면 프레임 별로 삭제합니다.

![jvm5](/assets/img/jvm5.png){:width="80%" height="80%"}

데이터 타입에 따라 Stack과 Heap에 저장되는 방식이 다릅니다.

- 기본 타입 변수는 Stack 영역에 직접 값을 가진다.
- 참조 타입 변수는 Heap 영역이나 Method 영역에 객체 주소를 가집니다.

스택 영역은 각 Thread마다 하나씩 존재하며, Thread가 시작될 때 할당되고 Thread가 종료하면 Runtime Stack도 사라집니다.
프로세스가 메모리에 로드될 때 스택 사이즈가 고정되어있어, 런타임 시 스택 사이즈를 바꿀 수는 없습니다. 만일 고정된 크기의 JVM 스택에서 프로그램 실행 중 메모리 크기가 충분하지 않다면 StackOverflowError가 발생합니다.



### PC Register

쓰레드가 어떤 부분을 무슨 명령으로 실행해야할지에 대한 기록을 하는 부분으로, 현재 수행 중인 JVM 명령의 주소를 저장하는 공간입니다.

쓰레드가 시작될 때 생성되며, 각 쓰레드가 생성될 때마다 하나씩 공간이 생성됩니다. 



### Native method stack

자바 코드가 컴파일되어 생성되는 바이트코드가 아닌 실제 실행할 수 있는 기계어로 작성된 프로그램을 실행시키는 영역입니다.

또한 자바 이외의 언어(C, C++, 어셈블리 등)로 작성된 네이티브 코드를 실행하기 위한 공간이기도 합니다.

- JIT 컴파일러에 의해 변환된 Native Code 역시 여기에서 실행된다고 보면 됩니다.
  - 일반적으로 메소드를 실행하는 경우 JVM 스택에 쌓이다가 해당 메소드 내부에 네이티브 방식을 사용하는 메소드가 있다면 해당 메소드는 네이티브 스택에 쌓입니다. 네이티브 메소드가 수행이 끝나면 다시 자바 스택으로 돌아와 다시 작업을 수행합니다.



### JNI(Java Native Interface)

자바가 다른 언어로 만들어진 애플리케이션과 상호작용할 수 있는 인터페이스를 제공하는 프로그램입니다.

JVM이 Native Method를 적재하고 수행할 수 있도록 합니다. 

실질적으로 제대로 동작하는 언어는 C, C++정도 밖에 없다고 합니다.



### Native Method Library

C, C++로 작성된 라이브러리입니다.

만일 헤더가 필요하면 JNI는 이 라이브러리를 로딩해서 실행합니다.



## 출처

- 자바의 정석

- 자바의 신

- https://steady-coding.tistory.com/305

- https://coding-factory.tistory.com/828

- https://inpa.tistory.com/entry/JAVA-%E2%98%95-JVM-%EB%82%B4%EB%B6%80-%EA%B5%AC%EC%A1%B0-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%98%81%EC%97%AD-%EC%8B%AC%ED%99%94%ED%8E%B8