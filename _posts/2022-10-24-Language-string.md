---
title: "[Java] String, StringBuffer, StringBuilder"
author: "김소연"
date: 2022-10-24 21:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## String

자바에서 제공하는 문자열을 위한 클래스이다.



#### 인스턴스 생성

생성자의 매개변수로 입력받는 문자열을 인스턴스 변수 `value`에 문자형 배열`char[]`로 저장한다.

```java
public final class String implements java.io.Serializable, Comparable {
  private char[] value;
  . . .
}
```

- 변경 불가능한 클래스(immutable class)
  - 생성된 인스턴스가 갖고 있는 문자열은 읽어올 수만 있고 변경할 수는 없다.
  - `+` 연산자를 이용해서 문자열을 결합하는 경우는 인스턴스 내의 문자열이 바뀌는 것이 아니라 새로운 문자열이 담긴 인스턴스가 생성되는 것이다.
  - 문자열을 다루는 작업이 많이 필요한 경우 변경 가능한 StringBuffer클래스를 사용하는 것이 좋다.



#### 문자열 저장 방법

1. 문자열 리터럴을 지정하는 방법
2. String클래스의 생성자를 사용해서 만드는 방법

```
String str1 = "abc";				// 문자열 리터럴 "abc"의 주소가 str1에 저장됨
String str2 = "abc";				// 문자열 리터럴 "abc"의 주소가 str2에 저장됨
String str3 = new String("abc");	// 새로운 String인스턴스를 생성
String str4 = new String("abc");	// 새로운 String인스턴스를 생성
```

![springaop](/assets/img/string.JPG){:width="80%" height="80%"}

- 문자열 리터럴은 이미 존재하는 것을 재사용하는 것이다.
- String클래스의 생성자를 사용한 경우는 new연산자에 의해 메모리가 할당되어 항상 새로운 인스턴스가 생성된다.



**문자열 비교**

- `equals()` : 문자열의 내용 비교
- `==` : String인스턴스의 주소를 비교



**문자열 리터럴**

자바 소스파일에 포함된 모든 문자열 리터럴은 컴파일 시에 클래스 파일에 저장된다.

- 같은 내용의 문자열 리터럴은 한번만 저장된다.
  - 문자열 리터럴도 String인스턴스고, 한번 생성하면 내용을 변경할 수 없으니 하나의 인스턴스를 공유하면 되기 때문이다.
- 클래스 파일에는 소스파일에 포함된 모든 리터럴의 목록이 있다.
  - 해당 클래스 파일이 클래스 로더에 의해 메모리에 올라갈 때, 이 리터럴 목록에 있는 리터럴들이
    JVM내에 있는 '상수 저장소(constanct pool)' 에 저장된다.



**빈 문자열(empty string)**

길이가 0인 char형 배열을 내부적으로 저장하고 있는 문자열

```java
String s = "";		// 빈 문자열로 초기화
char c = ' ';		// 공백으로 초기화
```

- C언어에서는 문자열 끝에 널문자가 붙지만, 자바에서는 널 문자를 사용하지 않고 문자열의 길이정보를 따로 저장한다.



### 출처📎

- 자바의 정석