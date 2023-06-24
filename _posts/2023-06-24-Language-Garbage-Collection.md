---
title: "[Java] Garbage Collection"
author: "김소연"
date: 2023-06-24 18:00:00 -0500
categories: [Language, Java]
tags: [Java]
---





## Garbage Collection

프로그램이 동적으로 할당했던 메모리 영역 중에서 필요없게 된 영역(Garbage)을 자동으로 해제하는 기능이다.

- 더 이상 필요없게된 영역이란, 어떤 변수로 가리키지 않게 된 영역을 의미한다.

- C, C++ 등의 프로그래밍 언어는 수동으로 메모리 관리를 해주어야 한다. 
  하지만 Java는 JVM의 Garbage Collector에서 불필요한 메모리를 알아서 정리해준다. 
- Java에서 명시적으로 불필요한 메모리를 표현하기 위해 null로 선언해준다.



## weak generational hypothesis

Java에서 Garbage Collector는 아래와 같은 두가지 전제 조건 하에 만들어졌다.

- 대부분의 객체는 금방 접근 불가능 상태(unreachable)가 된다.
- 오래된 객체에서 젊은 객체로의 참조는 아주 적게 존재한다.

즉, 객체는 대부분 일회성되며, 메모리에 오랫동안 남아있는 경우는 드물다는 것이다. 

그렇기 때문에 HotSpot VM에서는 객체의 생존 기간에 따라 Heap 영역을 크게 2개로 나누었다. 
둘로 나눈 공간이 Young 영역과 Old 영역이다.

### Young 영역

- 새롭게 생성한 객체의 대부분이 여기에 위치한다
- 대부분의 객체가 금방 접근 불가능 상태가 되기 때문에 매우 많은 객체가 Young 영역에 생성되었다가 사라진다. 
- 이 영역에서 객체가 사라질 때 Minor GC가 발생한다고 말한다.

### Old 영역

- 접근 불가능 상태로 되지 않아 Young 영역에서 살아남은 객체가 여기로 복사된다. 
- 대부분 Young 영역보다 크게 할당하며, 크기가 큰 만큼 Young 영역보다 GC는 적게 발생한다. 
- 이 영역에서 객체가 사라질 때 Major GC(혹은 Full GC)가 발생한다고 말한다.



Old 영역이 Young 영역보다 크게 할당되는 이유는 Young 영역의 수명이 짧은 객체들은 큰 공간을 필요로 하지 않으며 큰 객체들은 Young 영역이 아니라 바로 Old 영역에 할당되기 때문이다.



## GC 영역과 데이터 흐름

Garbage Collection 영역 별로 데이터 흐름은 아래와 같다.

![gc](/assets/img/gc.png){:width="80%" height="80%"}

Permanent Generation 영역은 Method Area 영역이라고도 한다. Method Area는 초기화 코드 정보들이나 상수 자료형을 저장하는 곳이다. 이 영역에서 GC가 발생할 수 있는데, 여기서 GC가 발생해도 Major GC 횟수에 포함된다.



### Old 영역에 있는 객체가 Young 영역의 객체를 참조하는 경우

Old 영역에는 512바이트의 덩어리(chunk)로 되어 있는 **카드 테이블(card table)**이 존재한다.

- 카드 테이블에는 Old 영역에 있는 객체가 Young 영역의 객체를 참조할 때마다 정보가 표시된다. 
- Young 영역의 GC를 실행할 때에는 Old 영역에 있는 모든 객체의 참조를 확인하지 않고, 이 카드 테이블만 뒤져서 GC 대상인지 식별한다.
- 카드 테이블은 Write Barrir를 사용하여 관리한다. Write Barrir는 Java Code를 해석하는 부분에 포함된 가벼운 연산으로, Old Generation에서 Young으로 가는 참조가 생기면 Card를 뒤집도록 하는 작업을 수행한다. 
  - write barrier는 Minor GC를 빠르게 한다. write barrirer때문에 약간의 오버헤드는 발생하지만 전반적인 GC 시간은 줄어들게 된다.



## Garbage Colection 동작과정

세부동작 방식은 영역별, 적용 알고리즘별로 다르지만 공통적으로 따르는 2단계는 다음과 같다.

> 1. Stop the world
> 2. Mark and Sweep



### Stop the world

가비지 컬렉션을 실행하기 위해 JVM이 애플리케이션의 실행을 멈추는 작업이다.
GC를 처리하는 쓰레드를 제외한 모든 쓰레드들의 작업을 중단시키고, GC가 완료되면 작업을 다시 재기한다.
애플리케이션 중단 시간을 최소화시키기 위해서 stop the world 작업 시간을 줄여야한다. 이러한 문제를 해결하기 위해 다양한 실행 옵션을 제공하고 있다.



### Mark and sweep

Stop the world 이후 GC가 스택의 모든 변수 또는 접근 가능한(reachable) 객체를 스캔한다.
Mark : 사용되지 않는 메모리를 식별하는 작업
Sweep : 사용되지 않는다고 식별된 메모리들을 해제하는 작업



## Young 영역 GC

### 구성

Young 영역을 3개의 영역으로 나뉜다.

- Eden 영역
- Survivor 영역 (2개)



### Minor GC 절차

1. **새로 생성한 대부분의 객체는 Eden 영역에 위치**한다.
2. Eden 영역에서 GC가 한 번 발생한 후 살아남은 객체는 Survivor 영역 중 하나로 이동된다.
3. Eden 영역에서 GC가 발생하면 이미 살아남은 객체가 존재하는 Survivor 영역으로 객체가 계속 쌓인다.
4. 하나의 Survivor 영역이 가득차게 되면 그 중에서 살아남은 객체를 다른 Survivor 영역으로 이동한다. 그리고 가득찬 Survivor 영역은 아무 데이터도 없는 상태가 된다.
5. 이 과정을 반복하다가 **계속해서 살아남아있는 객체는 Old 영역으로 이동**하게 된다.

Survivor 영역 중 하나는 반드시 비어있는 상태로 남아있어야 한다. 만약 두 Survivor 영역에 모두 데이터가 존재하거나, 두 영역 모두 사용량이 0이라면 정상적인 상황이 아니라고 생각하면 된다.



## Old 영역 GC

### 여러 GC 방식

JDK 7 기준

- Serial GC
- Parallel GC
- Parallel Old GC(Parallel Compacting GC)
- Concurrent Mark & Sweep GC (CMS)
- G1(Garbage First) GC



### Serial GC (-XX:+UseSerialGC)

Young 영역에서 GC는 앞에서 설명한 방식을 사용한다. 

Old 영역의 GC는 mark-sweep-compact라는 알고리즘을 사용한다.

> 1. Old 영역에 살아있는 객체를 식별(Mark)한다.
> 2. 힙(Heap)의 앞 부분부터 확인하여 살아있는 것만 남긴다.(Sweep)
> 3. 각 객체들이 연속되게 쌓이도록 힙의 가장 앞 부분부터 채워서 객체가 존재하는 부분과 객체가 없는 부분으로 나눈다. (Compaction)

**특징**

- 적은 메모리와 CPU 코어 개수가 적을 때 적합한 방식이다.
- 운영 서버에서는 절대 사용하면 안된다. 데스크톱의 CPU 코어가 하나만 있을 때 사용하기 위해 만든 방식이라 애플리케이션의 성능이 많이 떨어지게 된다.



### Parallel GC (-XX:+UseParallelGC)

Serial GC와 기본적인 알고리즘은 같다. 하지만 GC를 처리하는 쓰레드가 여러 개이다. Throughput GC라고도 부른다.
(Serial GC는 스레드 하나로 처리)

![gc2](/assets/img/gc2.png){:width="80%" height="80%"}

**특징**

- Serial GC보다 빠르게 처리할 수 있다.
- 메모리가 충분하고 코어의 개수가 많을 때 유리하다.
- Java 8 버전에서 default로 사용된다.



### Parallel Old GC (-XX:+UseParallelOldGC)

Young 영역에서 GC는 앞에서 설명한 방식을 사용한다. 

Old 영역에서는 Mark-Summary-Compaction 알고리즘을 사용한다.

> 1. Old 영역을 region별로 나누고, region별로 살아있는 객체를 식별(Mark)한다.
> 2. region 별로 살아있는 객체의 밀도가 높은 부분이 어디까지인지 dense prefix를 정한다. 오랜 기간 참조된 객체는 앞으로 사용할 확률이 높다는 가정하에 dense prefix를 기준으로 compact 영역을 줄인다.
> 3. compact 영역을 destination과 source로 나누고, 살아있는 객체는 destination으로 이동시키고 참조되지 않는 객체는 제거한다. (Compaction)

**특징**

- mark-sweep-compact 방식은 단일 스레드가 old 영역을 검사하는 방식이라면, 
  mark-summary-compact 방식은 여러 스레드를 사용해서 old 영역을 탐색한다.



### CMS GC (-XX:+UseConcMarkSweepGC)

![gc3](/assets/img/gc3.png){:width="80%" height="80%"}

> 1. **Initial Mark**
>    클래스 로더에서 가장 가까운 객체 중 살아있는 객체만 찾는다. (멈추는 시간이 매우 짧다)
> 2. **Concurrent Mark**
>    방금 살아있다고 확인한 객체에서 참조하고 있는 객체들을 따라가면서 확인한다. (다른 스레드가 실행 중인 상태에서 동시에 진행된다.)
> 3. **Remark**
>    Concurrent Mark 단계에서 새로 추가되거나 참조가 끊긴 객체를 확인한다.
> 4. **Concurrent Sweep**
>    쓰레기를 정리하는 작업을 실행한다. (다른 스레드가 실행되고 있는 상황에서 진행한다.)

**특징**

- stop-the-world 시간이 매우 짧다.
- 모든 애플리케이션의 응답 속도가 매우 중요할 때 사용하며, Low Latency GC라고도 부른다.

**단점**

- 다른 GC 방식보다 메모리와 CPU를 더 많이 사용한다.
- Compaction 단계가 기본적으로 제공되지 않는다.
  - 조각난 메모리가 많아 Compaction 작업을 실행하면 다른 GC 방식의 stop-the-world 시간보다 더 길어질 수 있기 때문에, Compaction 작업이 얼마나 자주 오랫동안 수행되는지 확인해야 한다.



### G1 GC (Garbage First GC)

![gc4](/assets/img/gc4.PNG){:width="80%" height="80%"}

> 1. 바둑판의 각 영역에 객체를 할당하고 GC를 실행한다.
> 2. 해당 영역이 꽉차면 다른 영역에서 객체를 할당하고 GC를 실행한다.

**특징**

- Young 영역에서 Old 영역으로 이동하는 단계가 사라진 방식이다.
- CMS GC를 대체하기 위해 만들어졌다.
- 지금까지 설명한 어떤 GC 방식보다도 빠르다.
- JDK 7에서 정식으로 포함하여 제공한다.






## 출처

- https://d2.naver.com/helloworld/1329
- [performeister.tistory.com - Hotspot JVM Garbage Collection의 기본 가정](https://performeister.tistory.com/60)

- [Serial GC, Parallel GC, Parallel Old GC 동작 원리 (tistory.com)](https://jgrammer.tistory.com/entry/JAVA-GC의-동작-원리-Serial-GC-Parallel-GC-Parallel-Old-GC)

- [Getting Started with the G1 Garbage Collector (oracle.com)](https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/G1GettingStarted/index.html)