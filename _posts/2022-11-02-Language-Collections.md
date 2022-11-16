---
title: "[Java] Collections Framework(컬렉션 프레임워크)"
author: "김소연"
date: 2022-11-01 21:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## Collections Framework

다수의 데이터를 쉽게 처리할 수 있도록 표준화된 방법을 제공하는 클래스의 집합

- 즉, 데이터를 저장하는 자료구조와 데이터를 처리하는 알고리즘을 구조화하여 클래스로 구현해 놓은 것입니다.



#### 컬렉션 프레임워크 핵심 인터페이스

![collections](/assets/img/collections.png){:width="80%" height="80%"}

- List와 Set을 구현한 컬렉션 클래스들은 서로 많은 공통부분이 있어서 공통된 부분을 다시 뽑아 Collection 인터페이스를 정의할 수 있었지만,
  Map인터페이스는 이들과는 전혀 다른 형태로 컬렉션을 다루기 때문에 같은 상속계층도에 포함되지 못했다.



| 인터페이스 | 특징                                       |
| ----- | ---------------------------------------- |
| List  | - 순서가 있는 데이터의 집합, 데이터의 중복을 허용한다.<br />- 구현클래스 : ArrayList, LinkedList, Stack, Vector 등 |
| Set   | - 순서를 유지하지 않는 데이터의 집합, 데이터의 중복을 허용하지 않는다.<br />- 구현클래스 : HashSet, TreeSet 등 |
| Map   | - 키(key)와 값(value)의 쌍(pair)으로 이루어진 데이터의 집합<br />- 순서는 유지되지 않으며, 키는 중복을 허용하지 않고, 값은 중복을 허용한다.<br />- 구현클래스 : HashMap, TreeMap, Hashtable, Properties 등 |

- 컬렉션 프레임워크의 모든 컬렉션 클래스들은 List, Set, Map 중의 하나를 구현하고 있다.
  - 구현한 인터페이스의 이름이 클래스의 이름에 포함되어 있어서 이름만으로도 클래스의 특징을 쉽게 알 수 있도록 되어있다.
  - Vector, Stack, Hashtable, Properties와 같은 클래스들은 컬렉션 프레임워크가 만들어지기 전부터 존재하던 것이기 때문에 컬렉션 프레임워크의 명명법을 따르지 않는다.
    - 이러한 기존의 컬랙션 클래스들은 호환을 위해, 설계를 변경해서 남겨두었지만 가능하면 사용하지 않는 것이 좋다. (대신 새로 추가된 ArrayList, HashMap을 사용하기)




## Iterator, ListIterator, Enumeration

컬렉션에 저장된 요소를 접근하는데 사용되는 인터페이스

- Enumeration : Iterator의 구버전 (가능하면 Iterator를 사용할 것)
- ListIterator : Iterator의 기능을 향상시킨 것



#### Iterator

Iterator 인터페이스를 통해 컬렉션의 요소를 읽어오는 방법을 표준화했다.

- 코드의 일관성을 유지하고 재사용성을 높인다. (객체지향을 이룬다.)

```java
public interface Iterator {
  boolean hasNext();
  Object next();
  void remove();
}
public interface Collection {
  ...
  public Iterator iterator();
  ...
}
```

컬렉션에 저장된 각 요소에 접근하는 기능을 가진 Iterator인터페이스를 정의하고,
Collection인터페이스에는 Iterator를 반환하는 iterator()를 정의하고 있다.



**컬렉션의 각 요소를 읽어오는 방법**

Collection인터페이스를 구현한 컬렉션 클래스에서 `Iterator`를 사용할 수 있다. (List, Set 인터페이스)

```java
Collection c = new ArrayList();	// 다른 컬렉션으로 변경 시 이 부분만 수정하면 된다.
Iterator it = c.iterator();

// c에 요소 추가하는 과정

while(it.hasNext()) {
  System.out.println(it.next());
}
```

1. 컬렉션 클래스에 대해 iterator()를 호출하여 Iterator를 얻는다.
2. 반복문을 사용해서 컬렉션 클래스의 요소들을 읽어온다.

- 참조변수의 타입을 Collection으로 하는 이유
  예를 들어 ArrayList에서 LinkedList로 바꿀 때 선언문 하나만 바꾸면 나머지 코드는 검토하지 않아도 되기 때문이다. 참조변수 타입이 Collection이므로 Collection에 정의되지 않은 메서드는 사용되지 않았을 것이기 때문이다.



**Map인터페이스를 구현한 컬렉션 클래스의 Iterator를 얻는 방법**

키와 값을 쌍으로 저장하기 때문에 iterator()를 직접 호출 할 수 없다.

```java
Map map = new HashMap();
Iterator it = map.entrySet().iterator();	// 아래 두 문장을 합친 것

// Set eSet = map.entrySet();
// Iterator it = eSet.iterator();
```

1. keySet()이나 entrySet()과 같은 메서드를 통해 Set 형태로 가져온다.
2. iterator()를 호출하여 Iterator를 얻는다.



#### ListIterator

컬렉션의 요소에 접근할 때 양방향으로 조회 가능하도록 기능 추가

- Iterator를 상속받아서 기능을 추가한 것
- List를 구현한 경우에만 사용 가능하다.

```java
ArrayList a = new ArrayList();	// 다른 컬렉션으로 변경 시 이 부분만 수정하면 된다.
ListIterator it = a.listIterator();

// a에 요소 추가하는 과정

while(it.hasNext()) {
  System.out.print(it.next());
}
System.out.println();

while(it.hasPrevious()) {
  System.out.print(it.previous());
}
System.out.println();
```



#### remove()

Iterator의 remove()는 단독으로 쓰일 수 없고, **next()와 같이 써야한다.**

- remove()를 호출하기 전에 반드시 next()가 호출된 상태여야 한다.
- 특정 위치의 요소를 삭제하는 것이 아니라 읽어온 위치의 요소를 삭제한다.


- next()의 호출없이 remove()를 호출하면 IllegalStateException이 발생한다.

```java
ArrayList a = new ArrayList();
Iterator it = a.iterator();

// a에 요소 추가하는 과정

while(it.hasNext()) {
  it.next();
  it.remove();
}
```



## Comparator, Comparable

Comparator 혹은 Comparable을 구현하면 객체들을 비교할 수 있게 된다.

- Wrapper 클래스와 String, Date, File 등의 클래스들은 Comparable을 구현하여 인스턴스들을 오름차순으로 정렬할 수 있다.

```java
public interface Comparator {
  int compare(Object o1, Object o2);
  boolean equals(Object obj);
}
public interface Comparable {
  public int compareTo(Object o);
}
```

- compare(), compareTo()는 두 객체가 같으면 0, 비교하는 값보다 작으면 음수, 크면 양수를 반환하도록 구현해야 한다.
- equals()는 Comparator를 구현하는 클래스는 오버라이딩이 필요할 수도 있다는 것을 알리기 위해서 정의한 것일 뿐 그냥 compare()만 구현하면 된다.



#### 차이

Comparable

- 자기 자신과 파라미터로 들어오는 객체를 비교
- compareTo() 메소드를 반드시 구현해야 한다.
- lang패키지에 있어서 import를 해줄 필요가 없다.

Comparator

- 파라미터로 들어오는 두 객체를 비교
- compare() 메소드를 반드시 구현해야 한다.
- util패키지에 있다.



#### 구현방법

Comparable 예시

```java
public class ClassName implements Comparable<ClassName> {
  int value;
  
  public int compareTo(ClassName o) {
    int thisVal = this.value;
    int anotherVal = o.value;
    
    // 방법1. 비교하는 값이 크면 -1, 같으면 0, 작으면 1을 반환한다. (권장)
    return (thisVal < anotherVal ? -1 : (thisVal == anotherVal ? 0 : 1));
    
    // 방법2. 두 비교대상의 값 차이를 반환한다. (Overflow, Underflow가 발생할 수 있으니 주의)
    // return thisVal - anotherVal;
  }
}
```



Comparator 예시

```java
import java.util.Comparator;	// import 필요
public class ClassName implements Comparator<ClassName> {
  int value;
  
  public int compare(ClassName o1, ClassName o2) {
    int o1Val = o1.value;
    int o2Val = o2.value;
    
    // 방법1. 비교하는 값이 크면 -1, 같으면 0, 작으면 1을 반환한다. (권장)
    return (o1Val < o2Val ? -1 : (thisVal == anotherVal ? 0 : 1));
    
    // 방법2. 두 비교대상의 값 차이를 반환한다. (Overflow, Underflow가 발생할 수 있으니 주의)
    // return o1Val - o2Val;
  }
}
```








### 출처📎

- 자바의 정석
- [TCPschool](http://www.tcpschool.com/java/java_collectionFramework_concept)
- [st-lab](https://st-lab.tistory.com/243)


