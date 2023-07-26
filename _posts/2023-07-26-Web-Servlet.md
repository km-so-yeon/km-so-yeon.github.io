---
title: "[Web] Reflection"
author: "김소연"
date: 2023-07-26 19:00:00 -0500
categories: [Web Application]
tags: [Web, Java]
---



## Servlet 이란?

Servlet(서블릿)이란 자바를 사용해서 웹 페이지를 동적으로 생성하는 서버 측 프로그램이다.



### Servlet 기반 웹 페이지 동작방식

![servlet](/assets/img/servlet.png){:width="80%" height="80%"}

1. 사용자(클라이언트)가 URL을 입력하면 웹 어플리케이션 서버(Web Application Server, WAS)가 HTTP Request를 Servlet 컨테이너로 전송한다.
2. 요청을 받은 Servlet Container는 HttpServletRequest, HttpServletResponse 객체를 생성한다.
3. web.xml 을 기반으로 사용자가 요청한 URL이 어느 서블릿에 대한 요청인지 찾는다.
4. 해당 서블릿에서 Service 메소드를 호출한 후 HTTP 방식(GET, POST)에 따라 doGet(), doPost() 메소드를 호출한다.
5. doGet(), doPost() 메소드에서 동적 페이지를 생성한 후 HttpServletResponse 객체에 응답을 보낸다.
6. 응답이 끝나면 HttpServletRequest, HttpServletResponse 두 객체를 소멸시킨다.



### Servlet 을 사용한 웹 페이지 개발 과정

1. 서블릿 규약에 따라 자바 코드를 작성한다.
2. 자바 코드를 컴파일해서 클래스 파일을 생성한다.
3. /WEB-INF/classes 폴더에 클래스 파일을 패키지에 맞춰서 위치시킨다.
4. web.xml에 서블릿 클래스를 설정한다.
5. 톰캣 등의 컨테이너를 실행한다.
6. 웹 브라우저에서 확인한다.



### 서블릿 클래스 구현 방법

1. `HttpServlet`을 상속받아야 한다.
2. HTTP 방식(get, post 등)에 따라 메소드를 재정의해야 한다. 
   - protected void **doGet**(HttpServletRequest req, HttpServletResponse res) throws ServletException, IOException
   - protected void **doPost**(HttpServletRequesponse resp) throws ServletException, IOException
3. 응답을 전송하려면 응답의 컨텐츠 타입을 지정한다. 
   - response.setContentType("text/html; charset=utf-8");
4. 응답을 전송하려면 PrintWriter 객체를 생성해야 한다. 
5. PrintWriter의 println 메소드를 통해 화면에 출력한다. 

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    // 요청에 대한 로직 수행
    // ...
    
    // HTML 응답 생성
    PrintWriter out = resp.getWriter();
    out.print("<html>");
    out.print("<head><title>제목</title></head>");
    out.print("<body><h1>안녕하세요</h1></body>");
    out.print("</html>");
    out.close();
}
```



### 서블릿 클래스 등록 방법

web.xml 파일에 `<servlet>`, `<servlet-mapping>` 태그를 사용해서 등록한다.

```xml
<servlet>
    <servlet-name>해당 서블릿을 참조할 때 이름</servlet-name>
    <servlet-class>서블릿으로 사용할 클래스 이름</servlet-class>
</servlet>
<servlet-mapping>
	<servlet-name>서블릿 이름</servlet-name>
	<url-pattern>매핑할 URL 패턴</url-pattten>
</servlet-mapping>
```



#### 어노테이션으로 매핑하기

서블릿 3.0버전부터 web.xml에 등록하지 않아도 `@WebServlet`어노테이션을 사용하면 자동으로 서블릿으로 등록된다.

- 만약 어노테이션을 사용하고 web.xml에 등록할 경우 둘다 매핑된다.

```java
@WebServlet(urlPatterns="매핑할 URL 패턴")
public class ServletClass extends HttpServlet { }
```



### Servlet Container

Servlet Container(서블릿 컨테이너)는 클라이언트의 요청(request)을 받아주고, 응답(response)할 수 있게 웹 서버와 소켓으로 통신한다.

- 예시 - 톰캣
  - 웹 서버와 통신하여 JSP(Java Server Page)와 Servlet이 작동하는 환경을 제공한다.

#### 역할

1. 웹 서버와의 통신 지원
   서블릿과 웹 서버가 손쉽게 통신할 수 있도록 한다.
2. 서블릿 생명주기(Life Cycle) 관리
   서블릿 클래스를 로딩하여 인스턴스화하고 초기화 메소드를 호출한다.
   요청이 들어오면 서블릿 메소드를 호출하고 서블릿의 생명이 다하면 Garbage Collection을 진행한다.
   (서블릿 클래스의 생명주기에 따른 메소드 : `init()` 👉 `doGet()` / `doPost()` 👉 `destroy()`)
3. 멀티 쓰레드 지원 및 관리
   요청이 올 때마다 새로운 자바 쓰레드를 생성하고, HTTP 서비스 메소드를 실행하고 나면 쓰레드가 자동으로 죽게 된다.
4. 선언적인 보안 관리
   일반적으로 보안관리를 XML 배포 서술자(web.xml)에 기록하기 때문에
   수정할 일이 생겨도 자바 소스 코드를 수정하여 다시 컴파일하지 않아도 된다.



위의 코드에서 볼 수 있듯이 Servlet 환경에서는 서블릿 클래스 안에 비즈니스 로직과 html이 분리가 안되는 문제가 있다. 이 문제를 해결하기 위해 JSP를 사용하게 되었다.

서블릿은 자바 코드 안에 HTML을 포함하고 있지만, JSP는 HTML 문서 안에 자바코드를 포함하고 있다.



## JSP(Java Server Page)

JSP 또한 자바를 사용해서 웹 페이지를 동적으로 생성하는 서버 측 프로그램이다.

`.jsp` 확장자를 사용한다.



### JSP 기반 웹 페이지 동작방식

1. 사용자의 브라우저는 `http://servername.../*.jsp` 형태로 요청을 한다.
2. 웹 어플리케이션 서버(Web Application Server, WAS)는 사용자의 요청을 JSP 컨테이너로 넘긴다.
   - 이미 JSP에 해당하는 서블릿이 있을 경우 5번으로 넘어간다. (변환단계, 컴파일단계 넘어가기)
3. JSP 페이지로부터 자바 코드를 생성한다. (변환단계)
4. 자바 코드를 컴파일해서 서블릿 클래스를 생성한다. (컴파일단계)
5. 서블릿에 사용자 요청을 전달한다.
6. 서블릿이 요청을 처리한 결과를 응답으로 생성한다.
7. 응답을 웹 브라우저에 전송한다.

- 변환 단계를 거친 자바 코드와, 컴파일 단계를 거친 클래스 파일은 톰캣 폴더 안에 생성된다.
- JSP 페이지를 변경하면 서블릿 클래스를 다시 생성한다.



## JSP 웹 어플리케이션의 모델

JSP를 사용해서 웹 어플리케이션을 개발하는 방법은 여러가지가 있다.



### 모델 1구조

![servlet3](/assets/img/servlet3.jpg){:width="80%" height="80%"}

JSP에서 모든 로직과 출력을 처리한다.

#### 동작

- 웹 브라우저의 요청을 JSP가 직접 받는다. 
- 웹 브라우저의 요청을 받은 JSP는 자바빈, 서비스 클래스를 사용해서 작업을 처리하고 그 결과를 클라이언트에 출력한다.

모델 1구조에서는 로직 코드와 결과 출력 코드가 아직도 섞여있다.
이를 해결하기 위해 모델2, MVC 구조가 나오게 되었다.



### 모델 2 구조

![servlet4](/assets/img/servlet4.jpg){:width="80%" height="80%"}

JSP에서 출력만 처리한다. 로직은 Java로 넘긴다.

#### 동작

- 웹 브라우저의 모든 요청을 서블릿이 받는다. 
  서블릿이 요청을 처리한 후 결과를 보여줄 JSP로 포워딩한다. 
  포워딩을 통해 요청 흐름을 받은 JSP는 결과 화면을 클라이언트에 전송한다.
- 웹 브라우저의 모든 요청을 단일 진입점(서블릿)에서 처리한다. 
  서블릿에서 웹 브라우저의 요청을 구분하는 방법이 필요하다.

MVC 패턴은 모델 2구조를 사용한다.



### MVC 구조 (Model-View-Controller)

![servlet2](/assets/img/servlet2.png){:width="80%" height="80%"}

컨트롤러가 요청을 받으면, 해당 로직을 처리할 모델을 선택한다. 
로직 처리가 끝나면 컨트롤러가 뷰를 선택한다. 
뷰에 내보낼 데이터는 모델에서 컨트롤러를 통해 받는다.

- 모델 : 비즈니스 영역의 로직을 처리한다.
- 뷰 : 비즈니스 영역에 대한 프레젠테이션 뷰를 담당한다.
- 컨트롤러 : 사용자의 입력 처리와 흐름 제어를 담당한다.



#### 구현

**컨트롤러**

- HttpServlet을 상속받은 Controller 클래스
- Controller 클래스에서 요청 URL과 서블릿 간에 매핑을 입력해주어야 한다. 

**뷰**

- JSP

**모델**

- 웹 브라우저의 요청을 처리하는 로직을 구현한다.
  1. 컨트롤러로부터 요청을 받는다.
  2. 비즈니스 로직 수행
  3. 수행 결과를 컨트롤러에 리턴한다.



## Spring Framework

JSP는 웹 페이지를 구성하는데 사용한다면, Spring은 웹 서비스를 만드는데 전반적인 환경을 제공한다.

더 나아가 Spring Boot에서는 관례적인 설정을 자동적으로 처리되도록 하였다.



## 결론

Sevlet에서부터 JSP, Spring Framework까지 반복된 코드를 없애려고하고
비즈니스 로직과 프레젠테이션 로직을 분리하고
요청을 받는 클래스와 로직을 구현하는 클래스를 분리하려고하면서 개발하기 편한 환경을 만들기위해 발전해왔다는 것을 알 수 있었다.



## 출처

- https://mangkyu.tistory.com/14
- 최범균의 JSP 2.3 웹 프로그래밍
- [위키백과 - 자바_서블릿](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94_%EC%84%9C%EB%B8%94%EB%A6%BF)

- https://cloudstudying.kr/lectures/235