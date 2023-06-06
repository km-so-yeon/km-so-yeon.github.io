---
title: "[Java] Nested 클래스"
author: "김소연"
date: 2023-06-06 22:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## Nested 클래스

클래스 안의 클래스

- 별도로 컴파일 할 필요가 없다. (감싸고 있는 클래스를 컴파일하면 자동으로 컴파일되기 때문이다.)
- 감싸고 있는 클래스에서 내부 클래스의 객체를 생성한 후 내부 클래스의 변수에 접근할 수 있다. (private 변수도)
- 컴파일한 후 Nested 클래스의 파일 이름 : `감싸고있는클래스명$내부클래스명.class`



###  Static nested 클래스

class 내에 class을 선언할 때 static으로 선언될 경우

```java
public class OuterOfStatic {
	static class StaticNested {
		// 내용 생략 ..
	}
}
```

- Static Nested 클래스는 외부 클래스의 Static 변수만 참조할 수 있다. 
  (그 외에는 컴파일 에러 발생)

- 생성

  ```java
  OuterOfStatic.StaticNested nest = new OuterOfStatic.StaticNested();
  ```



### 내부 클래스

class 내에 class을 선언할 때 static이 없을 경우

- 내부 클래스는 외부 클래스의 어떤 변수도 접근할 수 있다. (private으로 선언된 변수도 포함)

#### (로컬) 내부 클래스

이름이 있는 내부 클래스

```java
public class OuterOfInner {
	class Inner {
        
	}
}
```

- 생성

  ```java
  OuterOfInner outer = new OuterOfInner();
  OuterOfInner.Inner inner = outer.new Inner();
  ```

  감싸는 클래스의 객체를 미리 만들어야 한다.

#### 익명 내부 클래스

이름이 없는 내부 클래스

```java
public class OuterOfInner {
    // 내용 생략 ..
	public void anonymousTest() {
		Button button = new Button();
		// 익명클래스 시작
		EventListener listener = new EventListener() {
    		public void onClick() { ... }
		};
		// 익명클래스 끝
		button.setListener(listener);
	}
}
```

- 생성자를 호출한 후 바로 중괄호를 열어서 메소드 구현
- 객체를 해당 클래스 내에서 재사용하려면 객체를 생성한 후 익명클래스 사용

```java
public class OuterOfInner {
    // 내용 생략 ..
	public void anonymousTest() {
		Button button = new Button();
		button.setListener(new EventListener() {
            public void onClick() { ... }
        });
	}
}
```

- 객체를 해당 클래스 내에서 재사용할 필요가 없는 경우 익명클래스가 필요한 자리에서 바로 익명클래스 구현 및 사용

- 클래스를 많이 만들수록 메모리는 더 필요해지고, 애플리케이션을 시작할 때 더 많은 시간이 소요된다. 따라서 간단하게 객체를 생성할 수 있도록 했다.



### Nested 클래스를 사용하는 이유

> - 한 곳에서만 사용되는 클래스를 논리적으로 **묶어서 처리**할 필요가 있을 때 (Static Nested 클래스를 사용하는 이유)
>
> - 캡슐화가 필요할 때 (내부 클래스를 사용하는 이유)
>   - 하나의 클래스에서 어떤 공통적인 작업을 수행하는 클래스가 필요한데 다른 클래스에서는 그 클래스가 전혀 필요없을 때
> - 소스의 가독성과 유지보수성을 높이고 싶을 때



# 출처 📎

자바의 신