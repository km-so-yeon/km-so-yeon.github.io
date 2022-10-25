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




[String 메소드](https://www.w3schools.com/java/java_ref_string.asp)



**기본형과 문자열 간 변환방법**

- 기본형 → 문자열

  1. 빈 문자열 더해주기
  2. valueOf()

  ```java
  int i = 100;
  String str1 = i + "";
  String str2 = String.valueOf(i);
  ```

- 문자열 → 기본형

  1. parseInt()	// parse + 래퍼클래스
  2. valueOf()

  ```java
  int i = Integer.parseInt("100");
  int i2 = Integer.valueOf("100");
  ```

  - parseInt를 통해 Integer로 반환되지만 오토박싱에 의해 Integer가 int로 자동변환된다.
  - 래퍼 클래스 : 기본형 타입의 첫 글자가 대문자인 것



## StringBuffer

인스턴스를 생성할 때 지정된 문자열을 변경할 수 있다.

- 내부적으로 문자열 편집을 위한 버퍼(buffer)를 가지고 있다.



#### 인스턴스 생성

생성자의 매개변수로 입력받는 문자열을 인스턴스 변수 `value`에 문자형 배열`char[]`로 저장한다. 
(String과 유사)

- 이 배열은 문자열을 저장하고 편집하기 위한 공간(buffer)로 사용된다.

```java
public final class StringBuffer implements java.io.Serializable {
  private char[] value;
  . . .
}
```

- 생성 시 버퍼의 크기를 지정한다.
  - 버퍼의 크기를 지정할 때 충분히 잡아주는 것이 좋다. (편집 중인 문자열이 버퍼의 길이를 넘어서면 늘려주는 작업이 추가로 수행되어서 작업 효율이 떨어진다.)
  - 버커의 크기를 지정해주지 않으면 16개의 문자를 저장할 수 있는 크기의 버퍼를 생성한다.

```java
public StringBuffer(int length) {
  value = new char[length];
  shared = false;
}

public StringBuffer() {
  this(16);
}

public StringBuffer(String str) {
  this(str.length + 16);		// 지정한 문자열의 길이보다 16을 더 크게 버퍼를 생성한다.
  append(str);
}
```



#### 변경

버퍼의 크기가 작업하려는 문자열의 길이보다 작을 때 내부적으로 버퍼의 크기를 증가시키는 작업이 수행된다.

- 새로운 길이의 배열을 생성한 후 이전 배열의 값을 복사한다.
- 인스턴스변수 value는 길이가 증가된 새로운 배열을 참조하게 된다.

```java
// 새로운 길이(newCapacity)의 배열을 생성한다(newCapacity는 정수값)
char newValue[] = new char]newCapacity;

// 배열 value의 내용을 배열 newValue로 복사한다.
System.arraycopy(value, 0, newValue, 0, count);	// count는 문자열의 길이
value = newValue;		// 새로 생성된 배열의 주소를 참조변수 value에 저장
```



**append()**

기존 문자열에 새로운 문자열이 뒤에 추가되고, 자신의 주소를 반환한다.

```java
StringBuffer sb = new StringBuffer("abc");
sb.append("123");
StringBuffer sb2 = sb.append("ZZ");	// sb2는 sb와 같은 주소를 저장하게 된다.
System.out.println(sb);		// abc123ZZ
System.out.println(sb2);	// abc123ZZ
```

자신의 주소를 반환하기 때문에 **연속적으로 호출**할 수 있다.

```java
StringBuffer sb = new StringBuffer("abc");
sb.append("123").append("ZZ");
```



#### 비교

1. toString() 호출
2. equals() 비교

```java
String s = sb.toString();
String s2 = sb2.toString();
System.out.println(s.equals(s2));		// true

System.out.println(sb == sb2);			// false
System.out.println(sb.equals(sb2));		// false
```

- StringBuffer클래스에서는 equals메서드를 오버라이딩하지 않아서 등가비교연산자`==`로 비교한 것과 같은 결과를 얻는다.
  - toString()은 오버라이딩 되어있어서 toString()을 호출해서 문자열을 String으로 반환한 다음 equals메서드를 사용해서 비교해야 한다.



[StringBuffer 메소드](https://docs.oracle.com/javase/7/docs/api/java/lang/StringBuffer.html)



## String, StringBuilder, StringBuffer

**차이점**

String은 불변객체(immutable) 입니다.

StringBuilder는 가변객체이고, 비동기방식이어서 싱글스레드 환경에서 변화되는 문자열일 때 사용합니다. 비동기 방식이어서 속도가 빠릅니다.

StringBuffer는 가변객체이고, 동기방식이어서 멀티스레드 환경에서 변화되는 문자열일 때 사용합니다. (동기방식이므로 thread safe합니다.)




### 출처📎

- 자바의 정석