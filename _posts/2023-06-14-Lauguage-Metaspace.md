---
title: "[Java] Java 8 이후 메모리 변화"
author: "김소연"
date: 2023-06-14 19:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



![metaspace](/assets/img/metaspace.jpg){:width="80%" height="80%"}

Java 8이 나오면서 JVM의 메모리 영역 중에 Permanent Generation 영역이 사라지고 Metaspace 영역이 생겼다.



#### JVM에서의 PermGen(Java 7 이전), Metaspace(Java 8 이후)

![jvm2](/assets/img/jvm2.jpg){:width="80%" height="80%"}

PermGen과 Metaspace는 JVM Runtime Access Memory 중 **Method Area**에 해당하는 부분이다.
(JVM 벤터마다 다양하게 구현할 수 있지만, 오라클 HotSpot JVM 기준으로 했을 때)

필드와 메서드 정보, Runtime Constant Pool, Static 변수, 메서드의 바이트코드 등을 관리한다.

인스턴스 생성에 필요한 정보들을 관리하기 때문에 모든 스레드들이 공유하고 JVM이 시작될 때 생성된다.



## Permanent Generation

![metaspace2](/assets/img/metaspace2.png){:width="80%" height="80%"}

별도의 Permanent Generation Heap을 통해 관리되던 영역으로, JVM에서 크기를 설정해주어 관리해야 했다.

- Non-Heap Memory이고, (Heap 영역이 아닌) PermGen 영역에 해당한다.

아래와 같은 정보들이 저장되고 있었다.
- Class의 메타데이터 (바이트코드 포함)
- Method의 메타데이터
- static 객체, static 상수
- 상수화 된 String Object
- Class와 관련된 배열 객체 메타데이터
- JVM 내부적인 객체들과 JIT의 최적화 정보



Default로 제한된 크기를 가지고있는데 리사이징이 되지 않아 메타데이터들이 쌓이다보면 **OOME(Out of Memory Error)**를 일으킨다.

이를 해결하기 위해 Java 8부터 PermGen 영역이 사라지고 자바가 메모리 할당에 관여하지 않는 Native 메모리에 Metaspace가 생겼다.
- Java 8부터는 PermGen 관련 JVM 설정은 무시한다.



## Metaspace

![metaspace3](/assets/img/metaspace3.png){:width="80%" height="80%"}

이름에서 나타내는 것처럼 Metadata가 저장되며, Native 메모리 영역에 위치해서 OS에 의해 메모리를 동적으로 할당받는 영역이다. 

- 메모리를 필요한 만큼 늘릴 수 있어서 개발자들의 메모리 영역에 대한 제한을 최대한 보장할 수 있게되었다.

- PermGen은 고정된 크기로 설정되어있어 Full GC 작업이 많이 일어났지만 Metaspace는 필요에 따라 사이즈가 변동되기 때문에 효율적으로 GC작업을 할 수 있다고 한다.
  - Full GC 작업은 Stop-the-world 과정으로 애플리케이션이 잠시 중단될 수 있어서 최소화할 수록 좋다.

| JVM               | Defaut maximum PermGen size(MB) | Default maximum Metaspace size |
| ----------------- | ------------------------------- | ------------------------------ |
| 32-bit client JVM | 64                              | unlimited                      |
| 32-bit server JVM | 64                              | unlimited                      |
| 64-bit JVM        | 82                              | unlimited                      |



PermGen 영역에 있던 데이터들은 아래와 같이 변경되었다.

- Class의 메타데이터 (바이트코드 포함) → MetaSpace로 이동
- Method의 메타데이터 → MetaSpace로 이동
- static 객체, static 변수 (class variable) → **Heap으로 이동**
- 상수화된 String Object → **Heap으로 이동**
- Class와 관련된 배열 객체 메타데이터 → Metaspace로 이동
- JVM 내부적인 객체들과 JIT의 최적화 정보 → Metaspace로 이동

위와 같이 변경되면서 기존 PermGen 영역에 있던 **static object**들이 Heap 영역으로 옮겨지면서 **GC의 대상**이 될 수 있게 되었다.



#### 할당과 해제

할당은 클래스가 필요 시점에 로딩될 때 Class Loader에 의해 Metaspace에 클래스 정보가 저장된다.

해제는 Cloass Loader가 unload될 때만 일어난다. 

![metaspace4](/assets/img/metaspace4.png){:width="80%" height="80%"}

**어떤 live instance도 없고 어떤 참조도 없는 상태에서 GC가 일어난 이후에야 해제되는 것**이다.



## 결론

Heap 영역은 JVM이 관리하고 Native 영역은 OS에서 관리해 자동으로 크기를 조절하고, Metaspace가 Native 메모리를 사용함으로써 개발자는 메모리에서의 영역확보의 상한을 크게 인식할 필요가 없게 되었다.



## 출처

- [JVM의 Java 8에서의 변화 (becomeweasel.tistory.com)](https://becomeweasel.tistory.com/m/entry/JVM의-Java-8에서의-변화)
- https://www.programmersought.com/article/4905216600
- [Java 8에서 JVM의 변화 : PermGen이 사라지고 Metaspace가 등장하다. (goodgid.github.io)](https://goodgid.github.io/Java-8-JVM-Metaspace/)
- [자바 메타스페이스(Metaspace)에 대해 알아보자(jaemunbro.medium.com)](https://jaemunbro.medium.com/java-metaspace%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90-ac363816d35e)

- [[Java] PermGen 영역 대신 Metaspace가 도입된 Java 8 이후... (kth990303.tistory.com)](https://kth990303.tistory.com/418)

- [Permanent Generation 영역은 힙일까 아닐까?(blog.naver.com/simjunbo)](https://blog.naver.com/simjunbo/221080829920)