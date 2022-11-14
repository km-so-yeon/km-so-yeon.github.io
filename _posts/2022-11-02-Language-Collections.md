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







### 출처📎

- 자바의 정석
- [TCPschool](http://www.tcpschool.com/java/java_collectionFramework_concept)


