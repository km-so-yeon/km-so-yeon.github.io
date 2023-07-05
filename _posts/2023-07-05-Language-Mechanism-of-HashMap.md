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

하지만 HashMap은 보조 해시 함수(Additional Hash Function)을 사용하기 때문에,
보조 해시 함수를 사용하지 않는 HashTable에 비해 해시 충돌(Hash collision)이 덜 발생할 수 있어 성능상 이점이 있다.



## 보조 해시 함수(Addtional Hash Function)이란?

보조 해시 함수의 목적은 key의 해시값을 변경해 해시 충돌 가능성을 줄이는 것이다.



### Java의 HashMap에서 보조 해시 함수를 사용해야하는 이유

HashMap은 기본적으로 각 객체의 `hashCode()` 메서드가 반환하는 값을 사용하는데, 메서드 리턴 타입이 int다. 

int는 32비트 정수 자료형으로 **완전한 해시 함수를 만들 수 없다.** (완전한 해시 함수-전혀 충돌이 일어나지 않는 해시 함수)
논리적으로 생성 가능한 객체의 수가 2<sup>32</sup> 보다 많을 수 있고, 모든 HashMap 객체에서 O(1)을 보장하기 위해 랜덤 접근이 가능하게 하려면 원소가 2<sup>32</sup> 인 배열을 모든 HashMap이 가지고 있어야하기 때문이다. (메모리 낭비)



### 해시를 사용하는 associative array 구현체에서 

### 저장/조회할 해시 버킷을 계산하는 방법





## 출처

- https://d2.naver.com/helloworld/831311
- https://www.baeldung.com/java-hashmap
- https://ejyoo.tistory.com/72