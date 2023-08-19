---
title: "[Spring] 컴포넌트 스캔, 빈의 스코프"
author: "김소연"
date: 2023-08-19 19:00:00 -0500
categories: [Spring]
tags: [Spring]
---



## `@Component`(컴포넌트) 스캔

Spring 3.1부터 도입되었습니다.

`@ComponentScan`을 붙이고있는 클래스부터 컴포넌트 스캔을 시작합니다. 

- 스프링 부트에서 사용하는 `@SpringAppplication`이 이 `@ComponentScan` 인터페이스를 구현하고 있어서 
`@SpringApplication`을 가지고 있는 클래스부터 컴포넌트 스캔을 시작합니다.
- 어노테이션으로 스캔 범위를 지정할 수 있습니다. 해당 클래스가 있는 패키지까지 스캔해서 빈으로 생성합니다. (다른 패키지는 스캔을 안함 👉 의존성주입이 안됨)



### `@Filter`

- @ComponentScan의 속성인 includeFilter, excludeFilter에서 사용합니다.
- 클래스들을 스캔할 때 걸러낼 클래스를 설정할 수 있습니다.



### `@Component`를 구현하는 어노테이션들

- @Repository
- @Service
- @Controller
- @Configuration

위의 어노테이션들은 `@Compomnent`를 구현하므로 
위의 어노테이션을 사용하면 어플리케이션이 구동될 때 빈으로 등록됩니다.



### 컴포넌트 스캔 동작

어플리케이션 구동할 떄 모든 빈을 한번에 생성한다. 
👉 구동시간이 늘어난다.
👉 구동시간을 줄이고 싶을 떄 펑션을 사용해서 빈을 등록한다.



#### 펑션을 사용한 빈 등록

```java
public static void main(String[] args) {
	var app = new SpringApplication(등록할클래스명.class);
	app.addInitializers((ApplicationContextInitializer<GenericApplicationContext>) applicationContext -> {
		applicationContext.registerBean(등록할클래스명.class);
	});
	app.run(args);
}
```



## 빈의 스코프

빈의 스코프는 빈이 존재할 수 있는 범위를 뜻합니다. 스프링에서 아래와 같은 스코프를 지원합니다.

- 싱글톤
  - 애플리케이션 전반에 거쳐서 해당 빈의 인스턴스가 하나인 것
  - 변경하지 않을 경우 기본적으로 싱글톤으로 사용한다.
- 프로토타입 : 인스턴스의 스코프를 설정해주는 것 `@Scope("prototype")`
- 웹 관련 스코프
  - Request : 웹 요청이 들어오고 나갈 때까지 유지되는 스코프
  - Session : 웹 세션이 생성되고 종료될 때까지 유지되는 스코프
  - Application : 웹의 서블릿 컨텍스트와 같은 범위로 유지되는 스코프



### 싱글톤과 프토토타입 빈 사이의 참조

- 프로토타입 빈이 싱글톤 스콥의 빈을 참조해도 아무 문제가 없습니다.
- 싱글톤 빈이 프로토타입 빈을 참조하면 프로토타입의 빈이 새로 생성되지 않습니다.



#### 해결방법

- 빈을 선언할 때 - proxyMode 옵션 사용

  ```java
  @Scope(value="prototype", proxyMode=ScopedProxyMode.TARGET_CLASS)
  public class Proto { ..
  ```

  해당 클래스의 인스턴스를 감싸는 프록시 인스턴스를 만들어주고, 프록시 인스턴스가 빈으로 등록되게 된다. 프록시를 거쳐서 해당 클래스를 사용하도록하여 빈으로 참조할 때마다 새로 생성되도록 한다.

- 빈을 주입할 때 - ObjectFactory 클래스 사용

  ```java
  @Autowired
  private ObjectFactory<Proto> proto;
  ```

  

### 싱글톤 객체 사용 시 주의할 점

- 프로퍼티가 공유가 된다.(변수가 thread-safe 하지 않게 됨)
- ApplicationContext 초기 구동 시 인스턴스를 생성한다.



## 출처

- 인프런 - 스프링 프레임워크 핵심기술
- https://code-lab1.tistory.com/186