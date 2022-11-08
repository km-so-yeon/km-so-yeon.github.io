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




## ArrayList

- List 인터페이스를 구현한다.
  - 데이터의 저장순서가 유지되고 중복을 허용한다.
- Vector를 개선한 것이고, Vector의 구현원리와 기능적인 측면에서 동일하다. 
  - Vector는 기존에 작성된 소스와 호환성을 위해서 남겨두고 있는 것뿐이기 때문에, 
    Vector보다는 ArrayList를 사용할 것
- 배열을 이용한 자료구조이기 때문에 데이터를 읽어오고 저장하는 데는 효율이 좋지만,
  용량을 변경해야할 때 새로운 배열을 생성한 후 기존의 배열로부터 복사해와야해서 효율이 떨어진다.
  - 그래서 처음에 인스턴스를 생성할 때, 저장할 데이터의 개수를 잘 고려하여 충분한 용량의 인스턴스를 생성하는 것이 좋다.



```java
import java.util.*;

class ArrayListEx {
  public static void main(String[] args) {
    
    // ArrayList 생성
    ArrayList list1 = new ArrayList(10);	// 인자를 넘기지 않을 때는 기본 크기 10으로 생성
    
    // ArrayList 마지막에 객체 추가
    list1.add(new Integer(5));
    list1.add(new Integer(4));
    list1.add(new Integer(3));
    list1.add(new Integer(2));
    list1.add(new Integer(1));
    
    // list1에 1부터 2까지 저장된 객체를 반환
    ArrayList list2 = new ArrayList(list1.subList(1, 3));
    print(list1, list2);
    
    // 정렬
    Collections.sort(list1);
    print(list1);
    
    // 지정된 위치에 객체를 저장
    list1.set(4, "A");
    print(list1);
    
    list2.add("AA");
    list2.add("BB");
    list2.add("cc");
	print(list2);
    
    // list2에서 list1과 공통되는 요소들을 찾아서 삭제
    for(int i = list2.size()-1; i >= 0; i--) {
      if(list1.contains(list2.get(i))) {
        list2.remove(i);
      }
    }
    
    /* 동일한 코드
    for(int i = 0; i < list.size(); i++) {
    	if(list1.contains(list2.get(i))) {
    		list2.remove(i);
    		i--;
    	}
    }
    */
    print(list2);
    
  }
}
```

```
list1:[5, 4, 3, 2, 1]
list2:[4, 3]

list1:[1, 2, 3, 4, 5]

list1:[1, 2, 3, 4, A]

list2:[4, 3, AA, BB, CC]

list2:[AA, BB, CC]
```



#### 객체 삭제

객체를 삭제하면 삭제할 객체의 바로 다음에 있는 데이터를 한 칸씩 위로 복사해서 삭제할 객체를 덮어쓰는 방식으로 처리한다. 만일 삭제할 객체가 마지막 데이터라면, 복사할 필요없이 단순히 null로 변경해준다.

![collections](/assets/img/collections.png){:width="80%" height="80%"}

1. System.arraycopy(data, 3, data, 2, 2)
   data[3]에서 data[2]로 2개의 데이터를 복사하라는 의미
2. data[size-1] = null
   마지막 데이터를 null로 변경
3. size--;
   데이터가 삭제되어 데이터의 개수가 줄었으므로 size의 값을 -1 감소

| 메서드                             | 설명                                       |
| ------------------------------- | ---------------------------------------- |
| E remove(int index)             | 파라미터로 int를 전달하면 <br />해당 index의 객체를 삭제 후 해당 객체 return |
| boolean remove(Object e)        | 파라미터로 객체를 전달하면 해당 객체를 찾아서 <br />첫번째로 나오는 값만 삭제, <br />삭제했으면 true, 삭제할 값이 없으면 false return |
| boolean removeAll(Collection c) | Collection이 포함하고 있는 객체를 삭제               |
| boolean retainAll(Collection c) | Collection이 포함하고 있는 객체 외에 모두 삭제          |

- 숫자 객체 삭제 시
  - int를 전달하면 index의 객체를 삭제하기 때문에 Integer 객체로 넘겨서 해당 값을 삭제한다.
    `list.remove(Integer.valueOf(1));`








### 출처📎

- 자바의 정석
- [TCPschool](http://www.tcpschool.com/java/java_collectionFramework_concept)


