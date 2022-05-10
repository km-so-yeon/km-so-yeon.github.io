---
title: "[Spring] 스프링 MVC 동작 과정"
author: "김소연"
date: 2022-05-04 21:00:00 -0500
categories: [Web Application]
tags: [Spring]
---



## Spring 동작 과정

Spring 동작 순서 및 구조는 개발자의 개발방식에 따라 달라진다.



#### CASE 1

> Request -> **DispatcherServlet** (web.xml) -> **HandlerMapping** (servelet-context.xml) -> **Controller** (Controller -> Service -> DAO -> DB -> DAO -> Service -> Controller) -> **DispatcherServlet** -> **ViewResolver** -> **View** -> Response



![springmvc](/assets/img/springmvc.png){:width="80%" height="80%"}

1. 클라이언트가 Request 요청을 하면 **DispatcherServlet**이 요청을 가로챈다.
   이때, DispatcherServlet이 모든 요청을 가로채는 건 아니고 web.xml에 \<url-pattern>에 등록된 내용만 가로챈다.
2. DispatcherServlet이 가로챈 요청을 **HandlerMapping**에게 보내 해당 요청을 처리할 수 있는 **Controller**를 찾는다.
3. 실제 로직 처리(Controller -> Service -> DAO -> DB -> DAO -> Service -> Controller)
4. 로직 처리 후, **DispatcherServlet**에 View이름을 리턴한다.
5. **ViewResolver**를 통해 결과를 출력할 View 화면을 검색한다.
6. 처리결과를 View에 송신하고, View 화면을 최종 클라이언트에게 전송한다.
   - View는 받은 데이터를 다시 DispatcherServlet에 송신한다.
   - DispatcherServlet은 받은 데이터를 클라이언트로 전달한다.



#### CASE 2

![springmvc2](/assets/img/springmvc2.png){:width="80%" height="80%"}

1. 클라이언트가 URL로 접근하여 View로 정보를 요청한다.
2. View에서 해당 요청을 수행하려고 한다.
3. 이후 과정은 위와 같다.



## 구조 분석

![springmvc3](/assets/img/springmvc3.JPG){:width="80%" height="80%"}

#### 1. web.xml (DispatcherServlet)

```xml
<servlet>
	<servlet-name>appServlet</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	<init-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>/WEB-INF/spring/appServlet/servlet-context.xml</param-value>
	</init-param>
	<load-on-startup>1</load-on-startup>
</servlet>

<servlet-mapping>
	<servlet-name>appServlet</servlet-name>
	<url-pattern>/</url-pattern>
</servlet-mapping>
```

- web.xml(배포서술자)
- `/`로 시작한다면 모든 요청이 appServlet으로 매핑되는 것을 볼 수 있는데, `appServlet`이 바로 `DispatcherServlet`이다.
  이 때문에 사용자의 모든 요청이 DispatcherServlet을 통하게 된다.
- `init-param (초기화 파라미터)` 값으로 `/WEB-INF/spring/appServlet/servlet-context.xml` 을 가지고 생성한다.



#### 2. servlet-context.xml (HandlerMapping)

```xml
<beans:bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <beans:property name="prefix" value="/WEB-INF/views/" />
    <beans:property name="suffix" value=".jsp" />
</beans:bean>

<context:component-scan base-package="com.java.ex"/>
```

- `InternalResourceViewResolver` 는 사용자에게 보여줄 View를 찾기 위한 prefix(접두사)와 suffix(접미사)를 제공한다.
  예를 들어, Controller에서 'home'을 return할 경우 `/WEB-INF/views/` + `home` + `.jsp` 를 합쳐서 사용자에게 보여주게 된다.
- `component-scan` 은 @Component 어노테이션 뿐만 아니라 Streotype(@Controller, @Service, @Repository) 등을 자동으로 스캔하여 Bean으로 등록해주는 태그이다. 이 덕분에 @Controller 에게 요청을 전달할 수 있다.
  (base-package="패키지경로")



#### 3. Controller

![springmvc4](/assets/img/springmvc4.JPG){:width="80%" height="80%"}

1. base-package에서 @Controller가 부여된 Class를 찾는다.
2. @RequestMapping()의 value를 보고 요청한 url에 대응이 되면 해당 메소드가 실행된다.
3. 인자로 전달받은 model의 addAttribute("key", "value") 메소드를 통해 View에 값을 전달할 수 있다.
4. return에 적혀있는 View를 클라이언트에게 전달한다.



#### 4. ViewResolver

사용자에게 결과를 렌더링하여 보여주는 역할을 한다.

사용자에게 보여줄 View를 생성할 때 prefix, suffix를 지정하여 View이름과 결합해 해당 경로에 있는 View를 찾는다.



### 출처📎


- [https://hpark3.tistory.com/28]()
- [https://velog.io/@lsj8367](https://velog.io/@lsj8367/Spring-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%8F%99%EC%9E%91-%EA%B3%BC%EC%A0%95\)
- [https://hpark3.tistory.com/28]()
- [https://server-engineer.tistory.com/253]()
- [https://galid1.tistory.com/503]()
