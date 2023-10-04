---
title: "[Web] Spring API 공통 응답 포맷 만들기"
author: "김소연"
date: 2023-10-05 19:00:00 -0500
categories: [Web]
tags: [Web]
---



클라이언트가 API를 호출하고 응답을 받았을 때

원하는 결과 뿐만 아니라 실패했을 때 코드, 메시지 등의 응답에 대한 메타데이터를 같이 보내고 싶었고
메타데이터를 객체로 생성하여 공통적인 포맷을 만들어서 전달하고 싶었다.



## 필요한 객체

그러기 위해서 아래와 같은 클래스들을 생성해주어야 한다.

- Return 할 `Response 객체`
  - 공통 포맷을 가지기 위해 필요하다.
  - 모든 API에서 이 객체 형식으로 Return한다. (공통 포맷)
- 상태를 나타내는 `Status 객체`
  - 응답의 성공 여부, 코드, 메시지를 저장한다.
- 응답에 실패했을 때 발생시킬 `Exception 객체`
  - Exception을 발생시킬 때 Status의 코드를 사용한다.
- 모든 Controller에서 발생시키는 예외를 잡아서 처리하는 `ExceptionAdvisor 객체`
- 모든 Controller에서 Return하는 객체를 공통 포맷인 Response 객체를 사용해서 Return하는 `SuccessResponseAdvisor 객체`



## 동작 방식

#### 성공했을 때

> 1. `Controller`에서 결과 Return
> 1. `ExceptionHandler`에서 모든 Controller의 결과를 Response 객체로 리턴한다.



#### 실패했을 때

> 1. `Controller`에서 Status 객체을 가지고 Exception throw
> 2. `ExceptionHandler`에서 모든 Controller의 Exception을 가로채서 Response 객체로 리턴한다.



## Response 형식

| 필드명    | 타입       | 설명                                                |
| --------- | ---------- | --------------------------------------------------- |
| isSuccess | boolean    | 응답 성공 여부(성공했을 때 true, 실패했을 때 false) |
| code      | int        | 응답 코드                                           |
| message   | String     | 응답 메시지                                         |
| result    | T(Generic) | 응답 결과                                           |



## ExceptionAdvisor

Controller에서 매번 try-catch문을 통해 예외를 처리하면 가독성이 떨어지고 반복되는 코드가 생겨서 좋지 않다고 느껴졌다.

그래서 Controller에서 보내는 모든 예외를 처리하는 클래스를 만들어서 일괄적으로 모아서 관리할 수 있도록 핸들러를 만들었다.



#### 어노테이션 설명

- `@ControllerAdvice`  
  - 모든 `@Controller`에서 발생하는 예외를 잡아서 처리할 수 있다.
- `@RestControllerAdvice`  
  - `@ControllerAdvice`와 `@ResponseBody` 를 합쳐놓은 어노테이션이다.
  - `@ControllerAdvice`와  동일한 역할을 수행하고, `@ResponseBody`를 통해 객체를 리턴할 수도 있다.

👉 단순히 예외만 처리하고 싶다면 `@ControllerAdvice`를 적용하면 되고, 응답으로 객체를 리턴해야 한다면 `@RestControllerAdvice`를 적용하면 된다.



- 두 어노테이션 모두 패키지나 클래스 단위로 범위를 제한할 수 있다.

  ```java
  @RestControllerAdvice(basePackageClasses=EditController.class)
  ```

  ```java
  @RestControllerAdvice(basePackages="com.app.edit.controller")
  ```



#### 구현

`@RestControllerAdvice` 를 사용해서 컨트롤러의 응답을 잡아오고

`@ExceptionHandler`를 통해 생성된 예외가 있으면 처리한다.

```java
@RestControllerAdvice(basePackages = "com.board.controller")
public class ExceptionAdvisor {
    @ExceptionHandler(BaseException.class)
    public BaseResponse exceptionHandler(BaseException e) {
        return new BaseResponse(e.getStatus());
    }
}
```



## SuccessResponseAdvisor

Controller에서 보내는 모든 결과값들을 공통 응답 형식으로 처리할 수 있도록 핸들러를 만들었다.



#### 인터페이스 설명

- `ResponseBodyAdvice` 
  - `boolean supports(...)`
  - `Object beforeBodyWrite(...)`


ResponseBodyAdvice를 구현하기 위해 위 메소드들을 오버라이딩 해야 한다.

`supports()`는 Controller에서 작업이 끝난 Response를 `beforeBodyWrite()`로 보낼지 판단한다.
`returnType.getExecutable().getDeclaringClass()`를 통해 어떤 컨트롤러 클래스에서 왔는지 알아낼 수 있다.

`beforeBodyWrite()`에서 사용자가 원하는 작업을 처리한다.



#### 구현

```java
@RestControllerAdvice(basePackages = "com.board.controller")
public class SuccessResponseAdvisor<T> implements ResponseBodyAdvice<T> {

    @Override
    public boolean supports(MethodParameter returnType, Class converterType) {
        return true;	// Response를 beforeBodyWrite()로 보낸다.
    }

    @Override
    public Object beforeBodyWrite(Object body, MethodParameter returnType, MediaType selectedContentType, Class selectedConverterType, ServerHttpRequest request, ServerHttpResponse response) {

        return new BaseResponse(body);
    }
}
```



## ControllerAdvice를 2개 이상 사용하였을 때

`ExceptionAdvisor`와 `SuccessResponseAdvisor` 구현을 통해 ControllerAdvisor를 2개 이상 사용하게 되었다.

이 때 `@Order` 어노테이션을 통해 실행할 순서를 정해주어야 한다.

- 가장 먼저 실행 : `Ordered.HIGHEST_PRECEDENCE`
- 가장 나중에 실행 : `Ordered.LOWEST_PRECEDENCE`
  - @Order만 정의하거나, 아무 것도 정의하지 않으면 기본값으로 가장 나중 실행 순서로 지정된다.



#### 먼저 실행할 Bean 설정

```java
@RestControllerAdvice(basePackages = "com.board.controller")
@Order(Ordered.HIGHEST_PRECEDENCE)
public class ExceptionAdvisor { ...
```



#### 다음에 실행할 Bean 설정

```java
@RestControllerAdvice(basePackages = "com.board.controller")
@Order(Ordered.LOWEST_PRECEDENCE)
public class SuccessResponseAdvisor<T> implements ResponseBodyAdvice<T> { ...
```





# 출처

- https://velog.io/@leeeeeyeon/Spring-boot-Response-%ED%98%95%EC%8B%9D-%EB%A7%8C%EB%93%A4%EA%B8%B0
- https://velog.io/@banjjoknim/RestControllerAdvice
- https://chung-develop.tistory.com/61
