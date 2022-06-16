---
title: "[Spring] 스프링 PSA 개념"
author: "김소연"
date: 2022-06-16 21:00:00 -0500
categories: [Web Application]
tags: [Spring]
---



## PSA (Portable Service Abstraction)

일관성있는 서비스 추상화

- Service Abstraction
  - 어댑터 패턴을 적용해 같은 일을 하는 다수의 기술을 공통의 인터페이스로 제어할 수 있게 한 것
  - 추상화 계층을 사용해서 어떤 기술을 내부에 숨기고 개발자에게 편의성을 제공해주는 것
- Portable Service Abstraction
  - 서비스 추상화로 제공되는 기술을 다른 기술 스택으로 간편하게 바꿀 수 있는 확장성을 갖고 있는 것



### Spring이 제공하는 PSA

#### Spring Web MVC (Model View Controller)

```java
@Controller
class SampleController {			// controller
  
  @GetMapping("/sample")
  public String initForm(Map<String, Object> model) {
    Sample sample = new Sample();	// model
    model.put("sample", sample);
    return "sample/init";			// view
  }
}
```

- **@Controller** 을 사용하면 요청을 Mapping 할 수 있는 Controller 역할을 수행하는 Class라는 의미이다.

  그 Class에서 **@GetMapping, @PostMapping** 등으로 요청을 Mapping한다. (URL에 해당하는 요청이 들어왔을 때 메소드가 처리할 수 있도록 한다.)

  - Servlet을 Low level로 개발하지 않고도 Spring Web MVC를 사용하면 이렇게 Servlet을 간편하게 개발할 수 있다. 

    - servlet을 직접 구현했을 때 (doGet, doPost 직접 구현)

    ```java
    public class SampleCreateServlet extends HttpServlet {
      
      @Override
      protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws exception {
        super.doGet(req, resp);
      }
      
      @Override
      protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws exception {
        super.doPost(req, resp);
      }
    }
    ```

- PSA인 이유 : @Controller, @RequestMapping 등 을 사용하면 기존의 코드를 거의 변경하지 않고 웹 기술 스택을 바꿀 수 있다. (Servlet 혹은 Reactive로 코딩할 수 있고, 서버를 톰캣, 제티, 네티, 언더로우로 바꿔가며 쓸 수 있다.)




#### 스프링 트랜잭션 (Platform TransactionManager)

```java
public interface SampleRepository extends Repository<Sample, Integer> {
  
  @Transactional(readOnly = true)
  Sample findById(Integer id);
}
```

- `@Transactional` 이 붙어있는 메소드는 트랜잭션 처리가 된다.
- PSA인 이유 : `@Transactional`을 사용하면 JPA, JDBC, Hibernate 등 사용하는 기술에 따라 트랜잭션을 관리하는 구현체를 JpaTransactionManager, DatasourceTransactionManager, HibernateTransactionManager 로 바꿔가며 사용할 수 있다. (코드를 변경하지 않더라도)




#### 스프링 캐시 (CacheManager)

```java
public interface SampleRepository extends Repository<Sample, Integer> {
  
  @Transactional(readOnly = true)
  @Cacheable("samples")
  Collection<Sample> findAll() throws DataAccessException;
}
```

- `@Cacheable` 이 붙어있는 메소드는 캐시를 가져온다.
- PSA인 이유 : `@Cacheable`을 사용하면 캐시를 관리하는 구현체를 JCacheManager, ConcurrentMapCacheManager, EhCacheCacheManager로 바꿔가며 사용할 수 있다.




### 출처📎


- [인프런 - 예제로 배우는 스프링 입문](https://www.inflearn.com/course/spring_revised_edition#curriculum)
- [DR-Kim](https://dar0m.tistory.com/229)
