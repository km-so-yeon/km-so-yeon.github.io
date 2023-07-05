---
title: "[Java] HashMap은 어떻게 동작할까?"
author: "김소연"
date: 2023-07-05 18:00:00 -0500
categories: [Language, Java]
tags: [Java]
---





HashMap에 객체가 들어갔을 때 서로 다른 키로 저장되고, 
`get()`을 통해 값을 가져오는 내부적인 과정을 알아보려고 한다.

이 글은 Java 7, Java 8 기준이다.



Java에서는 Map 인터페이스를 구현한 클래스로 HashMap과 HashTable이 있고 제공하는 기능이 같다. 

HashMap과 HashTable을 정의한다면 '키에 대한 해시 값을 사용하여 값을 저장하고 조회하며, 키-값 쌍의 개수에 따라 동적으로 크기가 증가하는 associate array'라고 할 수 있다. (associate array == Map, Dictionary, Symbol Table 등)

하지만 HashMap은 보조 해시 함수(Additional Hash Function)을 사용하기 때문에,
보조 해시 함수를 사용하지 않는 HashTable에 비해 해시 충돌(Hash collision)이 덜 발생할 수 있어 성능상 이점이 있다.



## 보조 해시 함수(Addtional Hash Function)

보조 해시 함수를 사용하여 key의 해시값을 변경해 해시 충돌 가능성을 줄일 수 있다.
해시값의 분포가 가급적 균등하도록 한다.

JDK 1.4에 처음 등장했고 Java 5, 6, 7은 같은 방식의 보조 해시 함수를 사용하고 Java 8부터는 새로운 보조 해시 함수를 사용한다.



### Java의 HashMap에서 보조 해시 함수를 사용해야하는 이유

HashMap은 기본적으로 각 객체의 `hashCode()` 메서드가 반환하는 값을 사용하는데, 메서드 리턴 타입이 int다. 

int는 32비트 정수 자료형으로 **완전한 해시 함수를 만들 수 없다.** (완전한 해시 함수-전혀 충돌이 일어나지 않는 해시 함수)

논리적으로 생성 가능한 객체의 수가 2<sup>32</sup> 보다 많을 수 있고, 
모든 HashMap 객체에서 O(1)을 보장하기 위해 랜덤 접근이 가능하게 하려면 원소가 2<sup>32</sup> 인 배열을 모든 HashMap이 가지고 있어야하기 때문이다. (메모리 낭비)

> Boolean, Number 객체는 객체가 나타내려는 값 자체를 해시 값으로 사용할 수 있기 때문에 완전한 해시 함수 대상으로 삼을 수 있다. 하지만 String이나 POJO(plain old java object)에 대해 완전한 해시 함수를 제작하는 것은 사실상 불가능하다.



### 해시 버킷을 계산하는 방법

HashMap을 비롯한 많은 해시 함수를 사용하는 associative array 구현체에서는 메모리를 절약하기 위해 실제 해시 함수의 표현 정수 범위(N)보다 작은 M개의 원소가 있는 배열만을 사용한다. 

따라서 다음과 같이 객체에 대한 해시 코드의 나머지 값을 해시 버킷 인덱스 값으로 사용한다.

```java
int index = X.hashCode() % M;
```

이러한 방식을 사용하면, 서로 다른 해시 코드를 가지는 서로 다른 객체가 1/M의 확률로 같은 해시 버킷을 사용하게 된다. (같은 해시 버킷 인덱스를 가지면서 발생하는 해시 충돌)



### 해시 충돌을 해결하기 위한 다양한 자료구조

이처럼 해시 충돌이 발생하더라도 키-값 쌍 데이터를 잘 저장하고 조회할 수 있게 하는 방식으로는
Open Addressing, Separate Chaining이 있다.

![mechanismOfHashMap](/assets/img/mechanismOfHashMap.png){:width="80%" height="80%"}

#### Open Addressing

데이터를 삽입하려는 해시 버킷이 이미 사용 중인 경우 다른 해시 버킷에 해당 데이터를 삽입하는 방식

데이터를 저장/조회할 해시 버킷을 찾을 때에는 Linear Probing, Quadratic Probing 등의 방법을 사용한다.

#### Separate Chaining

각 배열의 인자는 인덱스가 같은 해시 버킷을 연결한 링크드 리스트의 첫 부분(head)이다.

#### 비교

둘 모두 Worst Case O(M) 이다.

- 캐시 효율 : Open Addressing > Separate Chaining
  Open Addressing이 연속된 공간에 데이터를 저장하기 때문에 캐시 효율이 더 높다.
  👉 데이터가 적을 때 Open Addressing 성능이 더 좋다.
- 하지만 배열의 크기가 커질 수록(M 값이 커질 수록) 캐시 적중률이 낮아진다.

#### Java HashMap에서 사용하는 방식

Java HashMap에서는 Separate Chaining 방식을 사용한다. 그 이유는 아래와 같다.

1. Open Addressing은 데이터를 삭제할 때 처리가 효율적이기 어려운데, HashMap에서 remove() 메서드는 매우 빈번하게 호출될 수 있기 때문이다.
2. HashMap에 저장된 키-값 쌍 개수가 일정 개수 이상으로 많아지면, Open Addressing이 더 느리다. 
   Open Addressing의 경우 해시 버킷을 채운 밀도가 높아질 수록 Worst Case 발생 빈도가 더 높아지기 때문이다.
3. Separate Chaining 방식의 경우 해시 충돌이 잘 발생하지 않도록 조정할 수 있다면 Worst Case 또는 Worst Case에 가까운 일이 발생하는 것을 줄일 수 있다.



## Java에서 해시 버킷 구현 변화

Java 2부터 Java 7까지 HashMap에서 Separate Chaining 구현 코드는 조금씩 다르지만, 구현 알고리즘 자체는 같았다. 

기존에는 Separate Chaining 에서 링크드 리스트를 고정적으로 사용했지만 Java 8 이후로는 데이터의 개수가 많아지면 트리를 사용하도록 변경되었다.

- 링크드 리스트를 사용할 때 조회에 대한 시간복잡도는 O(N/M)이지만
  트리를 사용할 때는 O(log N/M)이다.
  👉 일정 개수 이상일 때는 링크드 리스트 대신 트리를 사용하는 것이 성능 상 더 좋다.
- 링크드 리스트를 사용할 것인지, 트리를 사용할 것인지에 대한 기준은 하나의 해시 버킷에 할당된 키-값 쌍의 개수이다.
- 이 때 사용하는 트리는 Red-Black Tree이다. (TreeMap과 구현이 거의 같다.)

### 

### Java 8에서 트리 사용 기준

```java
static final int TREEIFY_THRESHOLD = 8;
static final int UNTREEIFY_THRESHOLD = 6;
```

Java 8 HashMap에서 트리 사용 기준을 상수 형태로 정하고 있다.

하나의 해시 버킷에 8개의 키-값 쌍이 모이면 링크드 리스트에서 트리로 변경한다.

만약 버킷에서 데이터를 삭제하면서 개수가 다시 6개가 되면 링크드 리스트로 변경된다. 트리는 링크드 리스트보다 메모리 사용량이 많고, 데이터의 개수가 적을 때 트리와 링크드 리스트의 Worst Case 수행 시간 차이 비교는 의미가 없기 때문이다.

8과 6으로 2 이상의 차이를 둔 이유는
차이가 1일 경우 어떤 한 키-값 쌍이 반복되어 삽입/삭제될 때 불필요하게 트리와 링크드 리스트를 변경하는 일이 반복되어 성능 저하가 발생할 수 있기 때문이다.



## 결론

- Java HashMap에서 해시 충돌을 방지하기 위해 Separate Chining과 보조 해시 함수를 사용한다.
- Java 8 부터는 Separate Chaining에서 데이터의 개수가 많아지만 링크드 리스트 대신 트리를 사용한다.





## 출처

- https://d2.naver.com/helloworld/831311
- https://dkswnkk.tistory.com/679
- https://www.baeldung.com/java-hashmap
- https://ejyoo.tistory.com/72
- https://dev-kimji1.medium.com/hash-dbcb1c51d84c