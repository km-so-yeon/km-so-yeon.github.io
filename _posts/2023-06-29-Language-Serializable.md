---
title: "[Java] Serializable"
author: "김소연"
date: 2023-06-29 18:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## Serializable

생성한 객체를 파일로 저장하거나, 파일로 저장한 객체를 읽거나
객체를 다른 서버로 보내거나, 다른 서버에서 생성한 객체를 받을 때
이 인터페이스를 구현해야 한다.

```java
public class SerialDto implements Serializable {
    static final long serialVersionUID = 1L;
	
    ...
}
```

`serialVersionUID` 값을 지정해주는 것을 권장한다.

- 별도로 지정하지 않으면 컴파일 될 때 자동으로 생성된다.

- 반드시 `static final long serialVersionUID`로 선언해야 한다.

- 서버끼리 객체를 주고받을 때 각 객체를 구분해주기 위한 값이다.
- 값은 아무 값이나 지정해줘도 되고, 필요에 따라 변경한다. 

- 클래스 이름이 같더라도 UID가 다르면 다른 클래스로 인식한다.

- 같은 UID여도 변수의 개수, 타입이 다르면 다른 클래스로 인식한다.



## 객체 저장하기

ObjectOutputStream 을 사용하면 객체를 저장할 수 있다.
(ObjectInputStream 을 사용하면 저장해놓은 객체를 읽을 수 있다.)

```java
public void saveObject(String fullPath, SerialDto dto) {
    FileOutputStream fos = null;
    ObjectOutputStream oos = null;

    try {
        fos = new FileOutputStream(fullPath);
        oos = new ObjectOutputStream(fos);	// fullPath에 있는 파일을 oos에 가져온다.
        oos.writeObject(dto);				// oos로 가져온 파일을 dto에 저장한다.
    } catch (Exception e) {
    ...
```

SerialDto에 Serializable을 구현하지 않고 객체를 저장하면 `NotSerializableException`이 발생한다.



## 객체 읽어오기

객체를 저장할 때는 Output을 사용하고, 읽어올 때는 Input을 사용한다.

```java
public void loadObject(String fullPath) {
    FileInputStream fis = null;
    ObjectInputStream ois = null;
    try {
        fis = new FileInputStream(fullPath);
        ios = new ObjectInputStream(fis);	// fullPath에 있는 파일을 ios에 가져온다.
        Object obj = ios.readObject();		// ios에 있는 객체를 obj로 가져온다.
        SerialDto dto = (SerialDto)obj;		// 객체를 SerialDto에 담는다.
        System.out.println(dto);
    } catch (Exception e) {
	...
```

변수를 추가하는 등 Serializable **객체가 변경되면  자동으로 생성되는 serialVersionUID가 다시 생성**된다.

이 과정에서 저장되어 있는 객체와 읽는 객체의 serialVersionUID가 달라지므로 `InvalidClassException`이 발생한다.

- serialVersionUID를 설정해줘서 같은 객체임을 나타낸다.
- 이렇게 같은 객체임을 나타냈을 때 저장되어있는 객체와 읽어온 객체의 변수의 개수, 타입이 다를 경우 해당 변수의 값으로 null이 들어가게된다.
- 그러므로 **객체가 바뀔 때마다 serialVersionUID를 변경**해주는 습관을 가져야 데이터에 문제가 발생하지 않는다. (그래야 Exception이 발생해서 객체가 바뀐 걸 알기 때문에)
- 요즘 대부분의 자바 개발 툴에서는 serialVersionUID를 자동 생성해주는 기능을 제공하기도 한다.



## transient

객체를 저장하거나, 다른 JVM으로 보낼 때, 
transient 라는 예약어를 사용해서 선언한 변수는 Serializable 대상에서 제외된다.

```java
public class SerialDto implements Serializable {
    
    transient private int bookOrder;
    
    ...
}
```

- 해당 객체를 생성한 JVM에서 사용할 때에는 이 변수를 사용해도 된다.
- 패스워드 같은 보안상 중요한 변수나 꼭 저장할 필요가 없는 변수에 사용한다.



## 출처

- 자바의 신