---
title: "[Spring] Bean 등록과 의존성 주입(DI) 사용법"
author: "김소연"
date: 2022-06-13 21:00:00 -0500
categories: [Web Application]
tags: [Spring]
---



#### 빈(Bean)

- Spring IoC Container가 관리하는 자바 객체
- ApplicationContext가 알고있는 객체 (ApplicationContext.getBean()으로 얻어질 수 있는 객체)
- Bean만 의존성 주입이 된다.





## Bean 등록

- Component Scanning
- Bean 설정파일에 직접 등록

### Component Scan

#### 원리

> 스프링에는 @Component 가 붙어있는 모든 클래스를 찾아서 클래스 인스턴스를 만들고 Bean으로 등록하는 Annotation Processor가 있다.

스프링부트는 main 메소드가 선언된 클래스를 기준으로 실행한다.

```java
@SpringBootApplication
public class MainApplication {
   public static void main(String[] args) {
      SpringApplication.run(MainApplication.class, args);
   }
}
```

해당 클래스의 `@SpringBootApplication` 은 스프링 부트의 가장 기본적인 선언을 설정한다.

- @SpringBootApplication 내의 @ComponentScan은 어노테이션을 스캔하여 Bean으로 등록해주는 어노테이션이다.

```
// ... 생략 ...

@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {

```

1.  `@ComponentScan` 이 붙어있는 위치에서부터 하위 패키지에 있는 모든 클래스를 찾아본다. (@ComponentScan은 어느 지점부터 찾을지 알려주는 것)

2. `@Component` 가 붙어있는 클래스를 찾아서 Bean으로 등록한다.

   @Component을 사용하는 Annotation

   - @Repository
   - @Service
   - @Controller
   - @Configuration
   - 등

- Repository가 Bean으로 등록되는 과정 (JPA가 제공하는 기능에 의해 등록)

  ```java
  public interface MemberRepository extends JpaRepository<Member, Long>{
  ```

  - 특정한 Annotation이 없더라도 Repository를 상속받은 경우 그 구현체를 찾아서 내부적으로 만들고 Bean으로 등록한다.




#### 등록방법

@Component 혹은 @Component을 사용하는 Annotation을 Class 위에 작성

```java
@Controller
public class CartController {
```



### Bean 설정파일에 직접 등록

- Bean 설정파일
  - xml
  - java



##### Java 설정파일

```java
@Configuration
public class SampleConfig {
  
  @Bean
  public SampleController sampleController() {
    return new SampleController();
  }
  
}
```

- 설정 클래스에 `@Configuration` 사용
- 객체를 return하는 메소드 위에 `@Bean` 사용 (return 하는 객체가 Bean으로 등록된다.)





## Bean 주입하기





### 출처📎


- [인프런 - 예제로 배우는 스프링 입문](https://www.inflearn.com/course/spring_revised_edition#curriculum)
- [밤둘레](https://bamdule.tistory.com/31)
- [절차대로 생각하고 객체로 코딩하기](https://codevang.tistory.com/256)
