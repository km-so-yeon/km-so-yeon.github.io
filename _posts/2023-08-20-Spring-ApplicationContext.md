---
title: "[Spring] ApplicationContext의 구현체"
author: "김소연"
date: 2023-08-20 19:00:00 -0500
categories: [Spring]
tags: [Spring]
---



ApplicationContext의 구현체인 `MessageSource`, `ApplicationEventPublisher`, `ResourceLoader`에 대해서 알아보도록 하겠습니다.



## MessageSource

메시지를 다국화할 수 있도록 하는 인터페이스이다. (국제화(i18n) 기능을 제공)

ApplicationContext가 구현하고 있는 기능 중 하나이다.
- `ApplicationContext extends MessageSource`
- ApplicationContext를 주입받을 수 있으면 MessageSource를 주입받을 수 있음.



스프링 부트를 사용한다면 별다른 설정 필요없이 messages.properties를 사용할 수 있다.

`ResourceBundleMessageSource`가 빈으로 자동으로 등록되어 messages라는 이름을 가진 리소스들을 자동으로 읽어서 Bundle로 만들어준다.



#### 예제

messages.properties

```properties
greeting=Hello {0}
```

messages_ko_KR.properties

```properties
greeting=안녕 {0}
```

MessageSource 사용

```java
public class AppRunner implements ApplicationRunner {

	@Autowired
	MessageSource messageSource;
	
	public void run(ApplicationArguments args) rhwos Exception {
		System.out.println(messageSource.getMessage("greeting", new String[]{"소연"}, Locale.KOREA));
		System.out.println(messageSource.getMessage("greeting", new String[]{"Soyeon"}, Locale.getDefault()));
	}
}
```





### 릴로딩 기능이 있는 메세지 소스 사용하기

- 운영 중에 메시지를 바꿀 수 있도록 한다.
- messageSource 메소드를 구현하여 리소스를 다시 읽도록 구현



#### 예제

messageSource 메소드를 구현

- ReloadableResourceBundleMessageSource 객체를 할당한다.

```java
@Bean
public MessageSource messageSource() {
	var messageSource = new ReloadableResourceBundleMessageSource();
	messageSource.setBasename("classpath:/messages");
	messageSource.setDefaultEncoding("UTF-8");	// 한글 인코딩
	messageSource.setCacheSeconds(3);	// 3초까지 캐싱하고 다시 리소스를 읽도록 설정
	return messageSource;
}
```

messageSource 사용

```java
public void run(ApplicationArguments args) throws Exception {
	while(true) {
		System.out.println(messageSource.getMessage("greeting", new String[]{"소연"}, Locale.KOREA));
		System.out.println(messageSource.getMessage("greeting", new String[]{"Soyeon"}, Locale.getDefault()));
		Thread.sleep(1000);	// console에 1초마다 찍도록한다.
	}
}
```

3초마다 한번씩 캐싱되어 메시지가 출력되도록 한다.



## ApplicationEventPublisher

이벤트 기반의 프로그래밍에 유용한 인터페이스이다. 옵저버 패턴의 구현체이다.

ApplicationContext가 구현하고 있는 기능 중 하나이다.

- `ApplicationContext extends MessageSource`
- ApplicationContext를 주입받을 수 있으면 MessageSource를 주입받을 수 있음.



### 구현방법

1. 이벤트 만들기
- `ApplicationEvent` 상속받기
- 스프링 4.2부터는 이 클래스를 상속받지 않아도 이벤트로 사용할 수 있다.
2. 이벤트 발생시키기
- publishEvent.publishEvent()
3. 이벤트 처리하기
- ApplicationListener\<이벤트\> 를 구현한 클래스를 만들어서 빈으로 등록하기
- 스프링 4.2부터는 @EventListener 를 사용해서 빈의 메소드에 사용할 수 있다.
- 기본적으로 synchronized



이벤트 객체 만들기

```java
public class MyEvent extends ApplicationEvent {
	private int data;
	

	public MyEvent(Object source) {
		super(source);
	}
	
	public MyEvent(Object source, int data) {
		super(source);
		this.data = data;
	}
	
	public int getData() {
		return data;
	}
}
```



이벤트 발생시키기

```java
@Component
public class AppRunner implements ApplicationRunner {

	@Autowired
	ApplicationEventPublisher publishEvent;
	
	public void run(ApplicationArguments args) rhwos Exception {
		// 이벤트 발생
		publishEvent.publishEvent(new MyEvent(this, 100));
	}
}
```



이벤트 처리하기

```java
@Component
public class MyEventHandler implements ApplicationListener<MyEvent> {
	@Override
	public void onApplicationEvent(MyEvent event) {
		// 이벤트 처리
		System.out.println("이벤트를 받았다. 데이터는 " + event.getData());
	}
}

```



### 스프링 4.2 이상일 경우

- 이벤트 발생 클래스에서 ApplicationEvent 상속받지 않아도 됨
- 이벤트 처리 클래스에서 ApplicationEventPublisher 구현하지 않아도 되지만 @EventListener를 추가해준다.
- 코드에 스프링 소스코드가 들어가지 않아서 비침투성을 충족시키는 코드가 된다. 
👉 POJO 기반의 프로그래밍
👉 테스트할 때 편해지고, 코드를 유지보수하기 쉬워진다.

이벤트 객체

```java
public class MyEvent {
	private Object source;

    public MyEvent(Object source, int data) {
        this.source = source;
        this.data = data;
    }

    public Object getSource() {
        return source;
    }

    public int getData() {
        return data;
    }
}
```



이벤트 처리하기

```java
@Component
public class MyEventHandler {
	@EventListener
	public void onApplicationEvent(MyEvent event) {
		// 이벤트 처리
		System.out.println("이벤트를 받았다. 데이터는 " + event.getData());
	}
}
```



### 여러 이벤트 핸들러를 두었을 때

- 핸들러가 순차적으로 실행된다. (순서는 보장하지 않는다.)
- 여러 이벤트 핸들러를 두었을 때
- 순서를 정하고 싶다면 `@Order` 사용하기

```java
@EventListener
@Order(Ordered.HIGHEST_PRACEDENCE + 2)
public void handle(MyEvent event) {
	System.out.println(Thread.currentThread.toString());
}
```

- 비동기적으로 실행하고 싶다면 `@Async` 사용하기
  - 메인 클래스에서 `@EnableAsync` 사용해주어야 한다.

  ```java
  @EventListener
  @Async
  public void handle(MyEvent event) {
  	System.out.println(Thread.currentThread.toString());
  }
  ```

  

### 스프링이 제공하는 기본 이벤트

- `ContextRefreshedEvent` : ApplicationContext를 초기화했거나 리프레시 했을 때 발생
- `ContextStartedEvent` : ApplicationContext를 start()하여 라이프사이클 빈들이 시작 신호를 받은 시점에 발생
- `ContextStoppedEvent` : ApplicationContext를 stop()하여 라이프사이클 빈들이 정지 신호를 받은 시점에 발생
- `ContextClosedEvent` : ApplicationContext를 close()하여 싱글톤 빈 소멸되는 시점에 발생
- `RequestHandledEvent` : HTTP 요청을 처리했을 때 발생



## ResourceLoader

리소스를 읽어오는 기능을 제공하는 인터페이스이다.

ApplicationContext가 구현하고 있는 기능 중 하나이다.
- `ApplicationContext extends ResourceLoader`
- ApplicationContext를 주입받을 수 있으면 ResourceLoader 주입받을 수 있음.



### 리소스 가져오기
`resourceLoader.getResource("경로");`

```java
@Component
public class AppRunner implements ApplicationRunner {

	@Autowired
	ResourceLoader resourceLoader;
	
	public void run(ApplicationArguments args) rhwos Exception {
		Resource resource = resourceLoader.getResource("classpath:test.txt");	// classes 패키지 아래 text.txt 가져오기
		System.out.println(resource.exists());
		System.out.println(resource.getDescription());	
		System.out.println(File.readString(Path.of(resource.getURI())));
	}
}
```



## 출처

- 인프런 - 스프링 프레임워크 핵심기술

  
