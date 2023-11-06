---
title: "[Java] Filter, Interceptor, AOP 비교"
author: "김소연"
date: 2023-11-06 19:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



자바 웹 개발을 할 때 **공통 처리**를 위해 Filter, Interceptor, AOP를 활용할 수 있다.



# Filter, Interceptor, AOP 흐름

![fia](/assets/img/fia.png){:width="80%" height="80%"}



### 실행 순서

Filter → Interceptor → AOP → Interceptor → Filter



### 비교

- Filter, Interceptor는 Servlet 단위에서 실행되고, AOP는 메소드 앞에서 Proxy  형태로 실행된다.
- Filter는 스프링 컨텍스트 외부에 존재하고 Interceptor, AOP는 스프링 컨텍스트 내부에 존재한다.



# 개념

##  Filter

- Servlet 3.x 부터 추가된 기술이다.
- Client와 Servlet 사이에서 전달되는 request와 response를 중간에서 가로채서 특정한 작업을 실행한다.
  - 클라이언트 → 필터 → 서블릿
  - 클라이언트 ← 필터 ← 서블릿
- 스프링 범위 밖에서 처리되고, 스프링 컨테이너가 아닌 톰캣과 같은 웹 컨테이너에 의해 관리가 된다. (스프링 빈으로 등록은 가능하다.)
- `ServletRequest`, `ServletResponse`를 제공하고, `HttpServletRequest`, `HttpServletResponse`로 다운캐스팅하여 사용할 수 있다.
  - Request와 Response를 조작할 수 있다.



### 구현

- `javax.servlet.Filter` 인터페이스를 구현한다.
- 메소드
  - `init(FilterConfig config)`
    서블릿 컨테이너가 필터 인스턴스를 초기화하기 위해 호출한다.
  - `doFilter(ServletRequest res, ServletResponse res, FilterChain chain)`
    필터에서 구현해야 하는 로직을 작성한다.
  - `destroy()`
    필터 인스턴스를 종료시키기 전에 호출한다.



```java
@WebFilter(filterName = "myFilter", urlPatterns = {"/*", "/post/*"})
public class MyFilter implements Filter {
    @Override
    public void doFilter(ServletRequest res, ServletResponse res, FilterChain chain) {
        
        // 클라이언트에서 서블릿으로 request를 넘기기 전에 실행되는 부분
        
        chain.doFilter(request, response);
        
        // 서블릿에서 클라이언트로 response를 넘기기 전에 실행되는 부분
        
    }
}
```



### 설정

필터가 어떤 URL 전 후로 실행될지 설정해주어야 한다.

- 방법1. 필터 클래스에서 `@WebFilter` 애노테이션을 사용한다.

  ```java
  @WebFilter(filterNamer = "myFilter", urlPatterns = {"/*", "/post/*"})
  public class MyFilter implements Filter {
  ```

- 방법2. `web.xml` 파일에 관련 정보를 추가한다.

  - `<filter>`, `<filter-mapping>` 태그를 사용한다.

  ```xml
    <filter>
      <filter-name>FilterName</filter-name>
      <filter-class>com.config.auth.MyFilter</filter-class>
      <init-param>
        <param-name>paramName</param-name>
        <param-value>value</param-value>
      </init-pram>
    </filter>
    
    <filter-mapping>
      <filter-name>FilterName</filter-name>
      <url-pattern>/*</url.patter>
    </filter-mapping>
  
    <filter-mapping>
      <filter-name>FilterName</filter-name>
      <url-pattern>/post/*</url.patter>
    </filter-mapping>
  ```

  



## Interceptor

- 스프링에서 DispatcherServlet과 Controller 사이에서 전달되는 HttpRequest와 HttpResponse를 중간에서 가로채서 특정한 작업을 실행한다.
- 스프링 컨텍스트 안에서 동작한다.



### 구현

![fia](/assets/img/fia2.png){:width="80%" height="80%"}

- 메서드
  - `preHandle(HttpServletRequest request, HttpServletResponse response, Object obj)`
    Controller가 호출되기 전에 실행된다. false를 리턴할 경우 Controller를 호출하지 않는다.
  - `postHandle(HttpServletRequest request, HttpServletResponse response, Object obj, ModelAndView view)`
    Controller가 실행된 후에 호출된다.
  - `afterCompletion(HttpServletRequest request, HttpServletResponse response, Object obj, Exception e)`
    View에서 최종 결과가 생성하는 일을 포함한 모든 일이 완료되었을 때 실행된다.



- 방법1. `HandlerInterceptor` 인터페이스를 구현한다.
  - 모든 메서드를 구현해야 한다.
- 방법2. `HandlerInterceptorAdapter` 클래스를 상속받는다.
  - 메서드가 이미 구현되어 있으므로, 필요한 메서드만 오버라이드한다.



### 설정

인터셉터가 어떤 URL 전 후로 실행될지 설정해주어야 한다.

- 방법1. `spring-servlet.xml` 설정 파일을 생성한다.

  ```xml
  <mvc:interceptor>
      	<!-- 인터셉터가 실행되어야 하는 URL -->
      	<mvc:mapping path="/" /> 
          <mvc:mapping path="/post/*" /> 
      
      	<!-- 인터셉터가 실행되지 않아야하는 URL -->
      	<mvc:exclude-mapping path="/logout"/>
      
          <bean class="com.config.auth.AuthInterceptor" />
  </mvc:interceptor>
  ```

- 방법2. `WebMvcConfigurer` 인터페이스를 구현한다.

  ```java
  @Configuration
  public class WebMvcConfig implements WebMvcConfigurer {
  
      private final AuthInterceptor authInterceptor;
  
      public WebMvcConfig(AuthInterceptor authInterceptor) {
          this.authInterceptor = authInterceptor;
      }
  
      public void addInterceptors(InterceptorRegistry registry) {
          registry.addInterceptor(authInterceptor).addPathPatterns("/", "/post/**");
      }
  
  }
  ```

  

## AOP

- AOP(Aspect Oriented Programming)의 약자, 관점 지향 프로그래밍
  - 소스 코드에서 반복해서 쓰는 코드들, 즉 공통된 로직을 모듈화한다. 이 공통된 로직을 관점(Aspect)라고 한다.
- 메소드 전후 지점 중에 원하는 지점을 설정해서 특정한 작업을 실행한다.



### 구현

- `@Aspect`를 붙여서 해당 클래스가 Aspect라는 것을 나타낸다.
- `@Component`를 붙여서 스프링 빈으로 등록한다.
- 포인트 컷을 지정한다.
  - `@Before` : 대상 메서드의 수행 전
  - `@After` : 대상 메서드의 수행 후
  - `@After-returning` : 대상 메서드의 정상적인 수행 후
  - `@After-throwing` : 예외 발생 후
  - `@Around` : 대상 메서드의 수행 전/후



## 정리

### Filter

- 서블릿 동작 전 후 과정에 부가적인 로직을 처리
- 웹 컨테이너에서 관리한다. 그래서 스프링과 무관하게 전역적으로 처리해야 하는 작업들을 처리
  - 인코딩 변환, 보안 검사(XSS 방어 등)
  - 예외 발생 시 Web Application에서 예외 처리
  - 스프링 컨테이너까지 요청이 전달되지 못하고 차단되므로 안정성을 더욱 높일 수 있다.
- ServletRequest, ServletResonse 객체를 조작할 수 있다.
  - 웹 애플리케이션에 전반적으로 사용되는 기능을 구현하기 적합

- web.xml 에서 적용한다.



### Interceptor

- 컨트롤러 동작 전 후 과정에 부가적인 로직을 처리
- 스프링 컨테이너에서 관리한다. 클라이언트 요청과 관련해서 전역적으로 처리해야하는 작업들을 처리할 수 있다.
  - 인증 및 인가(로그인 체크, 권한 체크 등)
- HttpServletReqeust, HttpServletResponse 등과 같은 객체를 제공받는다. (Request, Response 조작은 불가능)
  - 컨트롤러로 넘겨주기 위한 정보를 가공하는 기능을 구현하기 적합
- `servlet-context.xml` 에서 적용한다.



### AOP

- 메서드 동작 전 후 과정에 부가적인 로직을 처리한다.
- Interceptor와 Filter는 주소(URL)로 대상을 구분해서 처리하지만, AOP는 주소, parameter, annotation 등 PointCut이 지원하는 다양한 방법으로 대상을 지정할 수 있다.
- 비즈니스 단에서 세밀하게 조정하는 기능을 구현하기 적합
  - 로깅, 트랜잭션, 에러 처리 등






# 출처

- [https://goddaehee.tistory.com/154](https://goddaehee.tistory.com/154)
- [https://m.blog.naver.com](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=adamdoha&logNo=221665607853)
- [https://carnival.tistory.com/77](https://carnival.tistory.com/77)
- [https://velog.io/@miot2j](https://velog.io/@miot2j/Spring-Filter-Interceptor-AOP-차이-및-AOP를-사용하여-Logging을-구현한-이유)