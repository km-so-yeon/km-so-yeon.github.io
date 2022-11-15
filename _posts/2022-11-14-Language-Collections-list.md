---
title: "[Java] Collections Framework - List"
author: "김소연"
date: 2022-11-14 21:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## ArrayList

- List 인터페이스를 구현한다.
  - **데이터의 저장순서가 유지되고 중복을 허용**한다.
- Vector를 개선한 것이고, Vector의 구현원리와 기능적인 측면에서 동일하다. 
  - Vector는 기존에 작성된 소스와 호환성을 위해서 남겨두고 있는 것뿐이기 때문에, 
    Vector보다는 ArrayList를 사용할 것
- 배열을 이용한 자료구조이기 때문에 데이터를 읽어오고 저장하는 데는 효율이 좋지만,
  용량을 변경해야할 때 새로운 배열을 생성한 후 기존의 배열로부터 복사해와야해서 효율이 떨어진다.
  - 그래서 처음에 인스턴스를 생성할 때, 저장할 데이터의 개수를 잘 고려하여 충분한 용량의 인스턴스를 생성하는 것이 좋다.



#### 객체 삭제

객체를 삭제하면 삭제할 객체의 바로 다음에 있는 데이터를 한 칸씩 위로 복사해서 삭제할 객체를 덮어쓰는 방식으로 처리한다. 만일 삭제할 객체가 마지막 데이터라면, 복사할 필요없이 단순히 null로 변경해준다.

![collections](/assets/img/collections2.png){:width="80%" height="80%"}

1. System.arraycopy(data, 3, data, 2, 2)
   data[3]에서 data[2]로 2개의 데이터를 복사하라는 의미
2. data[size-1] = null
   마지막 데이터를 null로 변경
3. size--;
   데이터가 삭제되어 데이터의 개수가 줄었으므로 size의 값을 -1 감소



## LinkedList

![collections](/assets/img/collections3.png){:width="80%" height="80%"}

- **불연속적으로 존재하는 데이터를 서로 연결한 형태**로 구성되어 있다.
  - 배열처럼 데이터를 이동하기 위해 복사하는 과정이 없기 때문에 처리속도가 매우 빠르다.
- 배열의 단점을 보완하기 위해 고안되었다.
  - 배열의 단점
    1. 크기를 변경할 수 없다.
    2. 비순차적인 데이터의 추가 또는 삭제에 시간이 많이 걸린다. 
       (배열 중간에 데이터 추가 삭제 시 다른 데이터들을 복사해서 이동해야한다. 실행속도를 향상시키기 위해 충분히 큰 크기의 배열을 생성하게 되면 메모리가 낭비된다.)



#### 객체 삭제 및 추가

![collections](/assets/img/collections4.png){:width="80%" height="80%"}

객체를 삭제하기 위해서는
삭제하고자 하는 요소의 이전요소가 삭제하고자 하는 요소의 다음 요소를 참조하도록 변경하면 된다.

![collections](/assets/img/collections5.png){:width="80%" height="80%"}

객체를 추가하기 위해서는

1. 새로운 요소를 생성한 다음
2. 추가하고자 하는 위치의 이전 요소의 참조를 새로운 요소에 대한 참조로 변경해주고
3. 새로운 요소가 그 다음 요소를 참조하도록 변경하면 된다.



#### 더블 링크드 리스트(이중 연결리스트, doubly linked list)

![collections](/assets/img/collections6.png){:width="80%" height="80%"}

- 링크드 리스트는 이동방향이 단방향이기 때문에 다음 요소에 대한 접근이 쉽지만 이전 요소에 대한 접근이 어렵다. 
- 더블 링크드 리스트는 링크드 리스트에 참조변수를 하나 더 추가하여 **이전 요소에 대한 참조가 가능**하도록 한다. (그 외에는 링크드 리스트와 같다.)
  - 링크드 리스트보다 높은 접근성을 가진다.
- LinkedList클래스는 실제로는 더블 링크드 리스트로 구현되어 있다.

**더블 써큘러 링크드 리스트(이중 원형 연결리스트, doubly circular linked list)**

![collections](/assets/img/collections7.png){:width="80%" height="80%"}

- 더블 써큘러 링크드 리스트는 더블 링크드 리스트의 첫 번째 요소와 마지막 요소를 연결시킨 것이다.



#### ArrayList와 LinkedList 비교

| 컬렉션        | 읽기(접근시간) | 추가 / 삭제 | 비고                                 |
| ---------- | -------- | ------- | ---------------------------------- |
| ArrayList  | 빠르다      | 느리다     | 순차적인 추가 삭제는 더 빠름.<br />비효율적인 메모리사용 |
| LinkedList | 느리다      | 빠르다     | 데이터가 많을수록 접근성이 떨어짐                 |



## Stack과 Queue

#### Stack

- 마지막에 저장한 데이터를 가장 먼저 꺼내는 구조
- 한쪽에서 입력하고 삭제하기 때문에 ArrayList와 같은 배열 기반의 컬렉션 클래스가 적합
- Stack클래스로 구현하여 제공되고 있다.

#### Queue

- 처음에 저장한 데이터를 가장 먼저 꺼내는 구조
- 한쪽에서 입력하고 다른쪽에서 삭제하기 때문에 
  ArrayList이면 데이터를 꺼낼 때마다 데이터의 복사가 발생하므로 비효율적이기 때문에 
  데이터의 추가/삭제가 쉬운 LinkedList로 구현하는 것이 적합
- Queue인터페이스만 정의되어 있기 때문에 Queue인터페이스를 구현한 클래스들을 선택해서 사용하면 된다.
  - LinkedList, PriorityQueue 등 API문서 참고
- PriorityQueue
  - 저장한 순서에 관계없이 우선순위(Priority)가 높은 것부터 꺼내게 된다.
  - null은 저장할 수 없다. (NullPointerException 발생)
  - 저장공간으로 배열을 사용하고, 각 요소를 '힙'이라는 자료구조 형태로 저장한다.
- Deque(Double-Ended Queue)
  - 양쪽 끝에서 추가/삭제가 가능하다.
  - Deque의 조상은 Queue이고, 구현체는 ArrayDeque와 LinkedList 등이 있다.





### 출처📎

- 자바의 정석
- [TCPschool](http://www.tcpschool.com/java/java_collectionFramework_concept)

