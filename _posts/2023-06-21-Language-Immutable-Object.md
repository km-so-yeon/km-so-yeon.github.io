---
title: "[Java] Immutable Object"
author: "김소연"
date: 2023-06-21 18:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## Immutable Object란

객체가 생성된 이후 객체 내의 데이터들이 변할 수 없는 객체를 의미합니다.

- 재할당은 가능하지만, 한 번 할당하면 내부 데이터를 변경할 수 없습니다.
- 자바에서는 대표적인 예로 String, Integer가 있습니다.
- 반대개념으로는 Mutable Object(가변 객체)로, 생성 후에도 데이터를 변경할 수 있습니다.



## 배경

대부분의 객체지향 언어에서 객체는 참조 형태로 전달하고 받습니다. 
객체가 참조를 통해 공유된다면 어떤 장소에서 상태를 변경했을 때 모든 장소에서 영향을 받게됩니다. 이것이 의도한 동작이 아니라면 참조를 가지고 있는 다른 장소에 변경 사실을 통지하고 대처하는 추가 대응이 필요합니다.



## mutable 객체를 immutable 객체로 사용하는 방법

모든 필드를 `private`, `final` 키워드로 선언하면 불변 객체를 생성할 수 있습니다.

#### ASIS

```java
public class MutableObject {
	
	private int value;
	
	public MutableObject(int value) {
		this.value = value;
	}
	
	public void setValue(int newValue) {
		this.value = newValue;
	}
	
	public void getValue() {
		return value;
	}
}
```

위의 경우 setValue로 value값을 변경할 수 있기 때문에 가변 객체입니다.

#### TOBE

```java
public class ImmutableObject {
	
	private final int value;
	
	public ImmutableObject(int value) {
		this.value = value;
	}
	
	public void getValue() {
		return value;
	}
}
```

필드에 final 키워드를 사용했으므로 변수의 값을 변경하려고 하면 컴파일 에러가 발생합니다.
이렇듯 필드가 원시타입(Primitive Type)일 경우 `private`, `final` 키워드로 선언하면 값을 변경할 수 없기 때문에 불변 객체가 됩니다.



하지만 객체 내의 필드가 참조타입인 경우에는 추가적인 작업이 필요합니다.

### 참조타입인 경우

> - 모든 클래스 변수를 `private`, `final` 키워드로 선언한다.
> - 클래스를 `final`로 선언한다. (하위 클래스에서 overriding 하지 않기 위해)
> - 객체를 생성하기 위한 생성자 혹은 정적 팩토리를 추가한다.
>   (정적 팩토리 : 객체 생성을 담당하는 클래스 메서드)
> - 참조에 의해 변경가능성이 있는 경우 방어적 복사를 이용하여 전달한다.

위와 같은 4가지 규칙을 따를 경우 불변 객체를 만들 수 있습니다.



```java
public final class MutableObject {

	private final int age;
	private final String name;
	private final List<String> list;

	public MutableObject(int age, String name) {
		this.age = age;
		this.name = name;
		this.list = new ArrayList<>();
	}

	public int getAge() {
		return age;
	}
	
	public String getName() {
		return name;
	}
	
	public List<String> getList() {
		return list;
	}

}
```

위에서 내부 생성자를 만드는 대신 객체의 생성을 위해 정적 팩토리 메소드를 제공하고 있습니다.

하지만 모든 클래스 변수를 private, final로 선언하여도 참조타입의 변수는 아래와 같이 수정 가능성이 있습니다.

```java
MutableObject mo = new MutableObject(26, "SoYeon");
mo.getList.add("mintChoco");

System.out.println(mo.getList().size());	// 1
```

이처럼 클래스 변수에 참조 타입이 있는 경우는

1. 객체
2. Array
3. List

등이 있습니다.

### 1. 클래스 변수가 객체인 경우

#### ASIS

```java
public class Person {
	
    private final Age age;
    
    public Person(final Age age) {
        this.age = age;
    }
    
    public getAge() {
        return age;
    }
}

public class Age {
    
    private int value;
    
    public Age(final int value) {
        this.value = value;
    }
    
    public setValue(final int value) {
        this.value = value;
    }
    
    public getValue() {
        return value;
    }
}
```

```java
Age age = new Age(26);
Person person = new Person(age);
person.getAge().setValue(20);
```

이처럼 클래스 변수가 가변 객체일 경우 값을 변경할 수 있습니다.

#### TOBE

```java
public class Person {
	
    private final Age age;
    
    public Person(final Age age) {
        this.age = age;
    }
    
    public getAge() {
        return age;
    }
}

public class Age {
    
    private final int value;
    
    public Age(final int value) {
        this.value = value;
    }
    
    public getValue() {
        return value;
    }
}
```

이처럼 **클래스 변수를 불변 객체로** 만들어 해결할 수 있습니다.

- 모든 클래스 변수 private, final 키워드로 선언
- setter를 구현하지 않는다.

### 2. 클래스 변수가 Array인 경우

#### ASIS

```java
public class ArrayObject {
    
    private final int[] array;
    
    public ArrayObject(final int[] array) {
        this.array = array;
    }
    
    public getArray() {
        return array;
    }
}
```

```java
ArrayObject ao = new ArrayObject(new int[]{1, 2, 3});
ao.getArray()[0] = 10;	// [10, 2, 3]
```

배열을 그대로 참조하거나, 그대로 반환할 경우 이와 같이 외부에서 내부값을 변경시킬 수 있습니다.

#### TOBE

```java
public class ArrayObject {
    
    private final int[] array;
    
    public ArrayObject(final int[] array) {
        this.array = Arrays.copyOf(array, array.length);
    }
    
    public getArray() {
        return (array == null) ? null : array.clone();
    }
}
```

생성자에서 배열을 받아 **copy해서 저장**하도록 하고,
getter에서는 **clone하여 반환**하도록 하면 외부에서 값을 변경시킬 수 없습니다.

만약 int 처럼 원시타입이 아닌 객체라면 해당 객체는 불변객체이어야 합니다.

### 3. 클래스 변수가 List인 경우

#### ASIS

```java
public class ListObject {

	private final List<Animal> animals;
	
	public ListObject(final List<Animal> animals) {
		this.animals = animals;
	}
	
	public List<Animal> getAnimals() {
		return animals;
	}
}
```

```java
List<Animal> animalList = new ArrayList<>();
animalList.add("토끼");
animalList.add("판다");
ListObject lo = new ListObject(animalList);
lo.getAnimals().set(0, "사자");
```

List를 그대로 참조하거나, 그대로 반환할 경우 이와 같이 외부에서 내부값을 변경시킬 수 있습니다.

#### TOBE

```java
public class ListObject {

	private final List<Animal> animals;
	
	public ListObject(final List<Animal> animals) {
		this.animals = new ArrayList<>(animals);
	}
	
	public List<Animal> getAnimals() {
		return Collections.unmodifiableList(animals);
	}
}
```

생성자에서는 **새로운 list**를 만들고 값을 복사해서 저장하도록 하고
getter에서는 Collection의 `unmodifiableList()` 를 사용합니다.



이 외에도 여러 라이브러리의 메소드들을 통해 [immutable 객체를 만드는 방법](https://www.baeldung.com/java-immutable-list)이 있다.





## Immutable Object의 장단점

### 장점

- 객체에 대해 보안성, 신뢰성이 높아집니다. 값을 함부로 못바꾸기 때문에 믿고 사용할 수 있습니다.
- 객체 전체를 방어적 복사(defensive copy)하는 등 추가적인 대응이 필요가 없어집니다.
- Thread safe합니다. 
  - 다른 스레드에 의해서 특정 스레드의 데이터가 변경될 우려 없이 데이터에 접근할 수 있습니다. 병렬 프로그래밍에 유용하고, 동기화를 고려하지 않아도 됩니다.

- GC 성능을 높여줍니다.
  - 클래스가 살아있는 동안 final로 선언된 변수는 GC 스캔 대상에서 제외됩니다. 그렇기 때문에 GC의 스캔 범위 및 스캔 빈도수가 줄어들어서 GC의 성능을 높이는 결과를 가져옵니다.
    (만약 final로 선언하지 않아 가변 객체인 변수였을 경우 객체를 새로 세팅할 때마다 GC 스캔대상이 되므로 성능이 떨어지게 됩니다.)


### 단점

- 객체를 변경할 때마다 새로운 메모리를 할당해야히므로 메모리 누수가 발생하고, 이 때 추가적인 작업을 처리해야해서 성능 저하가 발생합니다.



## 결론

이처럼 불변 객체는 데이터에 대한 신뢰를 높일 수 있지만, 객체가 변경 가능한 데이터가 많은 경우에는 오히려 부적절한 경우가 있습니다. 이를 고려해서 가변과 불변 중 적절하게 선택해야합니다.



## 출처

- [위키백과 - 불변객체](https://ko.wikipedia.org/wiki/%EB%B6%88%EB%B3%80%EA%B0%9D%EC%B2%B4)
- [velog.io/@conatuseus - Immutable Object(불변객체)](https://velog.io/@conatuseus/Java-Immutable-Object%EB%B6%88%EB%B3%80%EA%B0%9D%EC%B2%B4)

- [망나니개발자](https://mangkyu.tistory.com/131)