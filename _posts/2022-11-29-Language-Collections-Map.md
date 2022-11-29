---
title: "[Java] Collections Framework - Map"
author: "김소연"
date: 2022-11-29 21:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## HashMap

- Map 인터페이스를 구현한다.
  - 키(key)와 값(value)을 하나의 데이터(entry)로 저장한다.
  - 해싱(hashing)을 사용한다. → 많은 양의 데이터를 검색하는데 뛰어난 성능을 보인다.




```java
public class HashMap extends AbstractMap implements Map, Cloneable, Serializable {
  transient Entry[] table;
  ...
  static class Entry implements Map.Entry {
    final Object key;
    Object value;
  }
}
```

- 키와 값을 하나의 클래스로 정의해서 하나의 배열로 다룬다. (데이터 무결성적인 측면에서 더 바람직하기 때문이다. + 객체지향)
- 키와 값을 각각 Object 타입으로 저장하여 어떠한 객체도 저장할 수 있다.
  - 키는 주로 String을 대문자 또는 소문자로 통일해서 사용한다.
- 키는 저장된 값을 찾는데 사용되는 것이기 때문에 컬렉션 내에서 유일해야 한다. (중복X)
  - HashMap에 저장된 데이터를 하나의 키로 검색했을 때 결과가 단 하나이어야 한다. (하나의 키에 여러 결과값이 나오면 원하는 값이 어떤 것인지 알 수 없기 때문이다.)
  - 중복되는 키에 값을 저장하면 기존의 값을 새로운 데이터로 덮어쓴다.




#### 객체 저장

- 객체를 저장할 때 이미 존재하는 키에 값을 저장하면 기존의 값을 새로운 데이터로 덮어쓴다.

  ```java
  HashMap map = new HashMap();

  map.put("A", 1234);
  map.put("B", 5678);

  map.put("A", 0);

  System.out.println(map.get("A"));	// 0
  System.out.println(map.get("B"));	// 5678
  ```


- 값(value)으로 HashMap을 다시 저장할 수 있다. (하나의 키에 다시 복수의 데이터를 저장할 수 있다.)

  ```java
  HashMap phoneBook = new HashMap();
  phoneBook.put("친구", new HashMap());

  HashMap group = (HashMap)phoneBook.get("친구");
  group.put("김철수", "010-1111-1111");
  group.put("한유리", "010-2222-2222");

  HashMap group = (HashMap)phoneBook.get("가족");
  group.put("봉미선", "010-3333-3333");
  group.put("신형만", "010-5555-5555");

  Set set = phoneBook.entrySet();
  Iterator it = set.iterator();

  while(it.hasNext()) {
    Map.Entry e = (Map.Entry)it.next();
    
    Set subSet = ((HashMap)e.getValue()).entrySet();
    Iterator subIt = subSet.iterator();
    
    System.out.println(e.getKey());
    
    while(subIt.hasNext()) {
      Map.Entry subE = (Map.entry)subIt.next();
      System.out.println(e.getKey() + " 번호 : " + e.getValue());
    }
    
    System.out.println();
  }

  /*
  친구
  김철수 010-1111-1111
  한유리 010-2222-2222

  가족
  봉미선 010-3333-3333
  신형만 010-5555-5555
  */
  ```






### 출처📎

- 자바의 정석


