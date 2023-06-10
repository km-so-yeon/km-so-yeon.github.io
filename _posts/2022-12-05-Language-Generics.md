---
title: "[Java] Generics(제네릭)"
author: "김소연"
date: 2023-06-10 21:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## Generic(제네릭)

클래스나 메소드에서 사용할 내부 데이터 타입을 컴파일 시 미리 지정하는 방법이다.

- **다룰 객체의 타입을 미리 명시해줌으로써, 번거로운 형변환을 줄여준다.**
  - 기존에는 다양한 종류의 타입을 다루는 메서드나 매개변수나 리턴타입으로 Object타입의 참조변수를 많이 사용했고, 그로 인해 형변환이 불가피했지만, 이제는 Object 타입 대신 원하는 타입을 지정하기만 하면 된다.
- 값을 지정할 때 미리 지정한 타입이 아닌 잘못된 타입으로 치환할 경우 컴파일이 안된다. (실행 시 잘못 형변환하여 예외가 발생할 일은 없다.)
- 데이터의 타입을 일반화한다(generalize)는 의미이다.
- JDK1.5에서 처음 도입되었다.



#### Object 타입을 사용해서 구현할 경우

- 값을 꺼낼 때 각각 형변환을 해주어야 한다.
- 변수의 타입이 어떤 타입인지 혼동될 수 있다. (instanceof를 통해 점검)

```java
public class CastingDto implements Serializable {
	private Object object
	public void setObject(Object object) { this.object = object };
	public void getObject() { return object; }
}
```

```java
public class GenericSample {
	public static void main(String[] args) {
		GenericSample sample = new GenericSample();
		sample.checkCastingDto();
		sample.checkDto();
		
	}
	
	public void checkCastingDto(); {
		// set
		CastingDto dto1 = new CastingDto();
		dto1.setObject(new StringBuffer());
		
		CastingDto dto2 = new CastingDto();
		dto2.setObject(new StringBuilder());
		
		// get
		StringBuffer temp1 = (StringBuffer)dto1.getObject();
		StringBuffer temp2 = (StringBuilder)dto2.getObject();
	}
	
	public void checkDto(CastingDto dto) {
		Object tempObject = dto.getObject();
		if(tempObject instanceof StringBuffer) {
			System.out.println("StringBuffer");
		} else if(tempObject instanceOf StringBuilder) {
			System.out.println("StringBuilder");
		}
	}
```




#### 장점

1. **타입 안정성을 제공한다.**
   의도하지 않은 타입의 객체가 저장되는 것을 막고, 저장된 객체를 꺼내올 때 원래의 타입과 다른 타입으로 잘못 형변환 되어 발생할 수 있는 오류를 줄여준다.
2. **타입체크와 형변환을 생략할 수 있으므로, 코드가 간결해진다.**




## Generic Class 선언

1. 클래스 옆에 **\<T>**를 붙인다.
2. Object를 모두 **T**로 바꾼다.

```java
class Box<T> {
  T item;
  
  void setItem(T item) { this.item = item; }
  T getItem() { return item; }
}
```

- T는 타입변수라고 하며, Type의 첫 글자에서 따온 것이다.
  - T가 아닌 다른 것을 사용해도 된다. (예 - Map\<K, V> == Key, Value)
  - 기호만 다를 뿐 임의의 참조형 타입을 의미한다는 것은 모두 같다.




#### 객체 생성

참조변수와 생성자에 타입 T 대신 사용될 실제 타입을 지정해주어야 한다.

```java
Box<String> b = new Box<String>();

b.setItem(new Object());	// 에러. String 이 외의 타입은 지정 불가
b.setItem("ABC");		// OK. String 타입이므로 가능

String item = b.getItem();	// 형변환 필요없음
```



Generic class이어도 예전의 방식으로 객체를 생성하는 것이 허용된다. 

```java
Box b = new Box();
b.setItem("ABC");		// 경고. unchecked or unsafe operation
```

- 제네릭 도입 이전의 코드와 호환을 위해 허용하였다.
- 다만 제네릭 타입을 지정하지 않아서 안전하지 않다는 경고가 발생한다.
  (unchecked or unsafe operation)
- Object타입을 지정하면, 타입을 지정하지 않은 것이 아니라 알고 적은 것이므로 경고는 발생하지 않는다.
- **반드시 타입을 지정해서 Generic과 관련된 경고가 나오지 않도록 할 것**



#### 용어

![generic](/assets/img/generic.png){: width="60%" height="60%"}

- Box\<T>  : 제네릭 클래스 ('T의 Box' 또는 'T Box'라고 읽는다.)
- T : 타입 변수 또는 타입 매개변수
- Box : 원시 타입

![generic](/assets/img/generic2.png){: width="60%" height="60%"}

- 제네릭 타입 호출 : 타입 매개변수에 타입을 지정하는 것 (Box\<String>은 컴파일 후 원시 타입인 Box로 바뀐다. == 제네릭 타입 제거)
- String : 대입된 타입



## 제네릭 타입의 이름 정하기

자바에서 정의한 기본 규칙 

꼭 지켜야 컴파일이 되는 것은 아니지만, 다른 사람들이 볼 때 쉽게 이해할 수 있도록 따르는 것이 좋다.

  - E : 요소(Element, 자바 Collection 에서 주로 사용됨)
  - K : 키
  - N : 숫자
  - T : 타입
  - V : 값
  - S, U, V : 두 번째, 세 번째, 네 번째에 선언된 타입



## `?` Wildcard 타입

특정 타입 대신 `?`를 적어주면 어떤 타입이 제네릭 타입이 되더라도 상관없다.

  - 메소드의 매개 변수로만 사용하는 것이 좋다.
    - 메소드 내에서는 instanceof 로 제네릭 타입 확인

    ```java
    public void wildcardStringMethod(Box<?> b) {
    	Object value = c.getItem();
    	if(value instanceof String) {
    		System.out.println(value);
    	}
    }
    ```

  - 어떤 객체를 와일드카드로 선언하고 객체의 값을 가져올 수는 있지만,
    와일드카드로 객체를 선언했을 때에는 특정 타입으로 값을 지정하는 것은 불가능하다.

    ```java
    public void callWildcardMEthod() {
    	Box<?> wildcard = new Box<String>();
    	wildcard.setItem("A");	// 에러 발생 - 알 수 없는 타입에 String을 지정할 수 없다
    }
    ```



## 제네릭 선언에 사용하는 타입의 범위 지정

`? extends 타입`

  - 제네릭 타입으로 해당 타입을 상속받은 모든 클래스를 사용할 수 있다.
  - Bounded Wildcards : Bound는 경계라는 의미도 있기 때문에, 매개 변수로 넘어오는 제네릭 타입의 경계를 지정하는 데 사용한다는 의미로 해석하면 된다.

```java
public class Car {
	protected String name;
	public Car(String name) { this.name = name; }
	public String toString() { return "Car name=" + name; }
}
```

```java
public class Bus extends Car {
	public Bus(String name) { super(name); }
	public String toString() { return "Bus name=" + name; }
}
```

```java
public void boundedWildcardMethod(Box<? extends Car> c) {
	Car value = c.getItem();
	System.out.println(value);
}
  
public void callBusBoundedWildcardMethod() {
	Box<Bus> wildcard = new Box<Bus>();
	wildcard.setItem(new Bus("6900"));
	boundedWildcardMethod(wildcard);		// Bus name=6900
}
```



## 메소드를 제네릭하게 선언하기

- wildcard로 메소드를 선언하면 매개 변수로 사용된 객체에 값을 추가할 수가 없다.
    - wildcard 처럼 타입을 두리뭉실하게 하는 것 보다 명시적으로 메소드 선언 시 타입을 지정해주면 보다 견고한 코드를 작성할 수 있다.
- 메소드의 리턴 타입 앞에 `<T>` 제네릭 타입을 선언해놓으면, 매개 변수에서 그 제네릭 타입이 포함된 객체를 받아서 처리할 수 있다.

```java
public <T> void genericMethod(Box<T> c, T addValue) {
	c.setItem(addValue);
    T value = c.getItem();
	System.out.println(value);
}
```

- Bounded Generic : 타입의 범위를 지정할 수 있다.

  ```java
  public <T extends Car> void boundedGenericMethod(Box<T> c, T addValue)
  ```

- 한 개 이상의 제네릭 타입은 콤마로 구분하여 나열하면 된다.

  ```java
  public <S, T extends Car> void multiGenericMethod(Box<T> c, T addValue, S another)
  ```

  



### 출처📎

- 자바의 정석
- 자바의 신
