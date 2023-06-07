---
title: "[Java] Annotation"
author: "김소연"
date: 2023-06-07 21:00:00 -0500
categories: [Language, java]
tags: [java]
---



## 어노테이션 Annotation

클래스나 메소드 등 선언 시에 @를 사용하는 것

- JDK 5부터 등장
- 언제 사용하는지?
  - 컴파일러에게 정보를 알려주거나
  - 컴파일할 때와 설치 시 작업을 지정하거나
  - 실행할 떄 별도의 처리가 필요할 때



## 사용가능한 어노테이션 (JDK 6까지 기준)

### @Override

- 해당 메소드가 부모 클래스에 있는 메소드를 Override했다는 것을 명시적으로 선언한다.

- 컴파일할 때 Override한 거니까 잘못 코딩했으면 알려준다.

### @Deprecated

- 클래스나 메소드가 더 이상 사용되지 않는다고 알려줄 떄 선언한다.

- 나중에 해당 클래스나 메소드를 누가 사용할 때 경고를 해준다. (사용해도 에러는 안나고 컴파일이 잘된다.)
- 하위 호환성을 위해 지우지않고 어노테이션을 통해 알려준다.

### @SupressWarnings

- 일부러 이렇게 코딩했다고 컴파일러에게 알려줄 때 선언한다.
- 컴파일러가 경고를 해주지않는다.





## 메타 어노테이션 Meta annotation

어노테이션을 선언하기 위한 어노테이션

- java.lang.annotation 패키지에 선언되어 있다.

#### @Target

- 어노테이션을 어떤 것에 적용할지 선언할 때 사용한다.

#### @Retention

- 얼마나 오래 어노테이션 정보가 유지되는지 선언할 때 사용한다.

#### @Documented

- 어노테이션에 대한 정보가 Javadocs(API)문서에 포함된다는 것을 선언할 때 사용한다.

#### @Inherited

- 모든 자식 클래스에서 부모 클래스의 어노테이션을 사용 가능하다는 것을 선언한다.





## 어노테이션 선언

- `@interface` : 어노테이션을 선언할 때 사용한다.
- 두 개 이상의 어노테이션을 선언할 때 중괄호를 한 후 쉼표로 구분해주면 된다.

```java
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface UserAnnotation {
	public int number();
	public String text() default "This is first annotation";
}
```

- @Target({ElementType.METHOD, ElementType.TYPE}) : 메소드와 타입(클래스, 인터페이스, enum 등)에 사용할 수 있다고 지정된 것
- @Retention(RetentionPolicy.RUNTIME) : 실행 시 이 어노테이션을 참조한다.
- default 뒤에 있는 값이 어노테이션을 사용할 때 기본값이 된다. (값을 지정하지 않아도 default값으로 지정된다.





## 어노테이션 사용

@어노테이션명(메소드명1=값1, 메소드명2=값2 ...)

```java
@UserAnnotation(number=0)
public UserAnnotationSample {
	public static void main(String args[]) {
		UserAnnotationSample sample = new UserAnnotationSample();
	}

	@UserAnnotation(number=1)
	public void annotationSample1() { }

	@UserAnnotation(number=2, text="second")
	public void annotationSample2() { }
}
```

- @Target 어노테이션에서 지정한 대상이 아닌 대상에 사용할 경우 컴파일 시 에러가 발생한다.



## 어노테이션에 사용한 값 확인

리플렉션 API에서 제공하는 Class, Method 사용

```java
public class UserAnnotationCheck {
	public static void main(String args[]) {
		UserAnnotationCheck sample = new UserAnnotationCheck();
		sample.checkAnnotations(UserAnnotationSample.class);
	}

	public void checkAnnotations(Class useClass) {
        // 해당 클래스에 선언되어 있는 메소드들의 목록을 리턴
		Method[] methods = useClass.getDeclaredMethods(); 
		for(Method tempMethod : methods) {
		// 해당 메소드에 선언되어있는 매개변수로 넘겨준 어노테이션이 있는지 확인하고, 
		//있을 경우 그 어노테이션의 객체를 리턴해준다.
    		UserAnnotation annotation = tempMethod.getAnnotation(UserAnnotation.class);
			if(annotation != null) {
				int number = annotation.number();
				String text = annotation.text();
				System.out.println(tempMethod.getName() + "() number=" + number + " text=" + text);
			}
		}
	}
}
```

**출력결과**

annotationSample1 number=1 text=This is first annotation
annotationSample2 number=2 text=second

#### 어노테이션은 상속이 안된다.



## 출처 📎

자바의 신