---
title: "[Java] DAO, DTO, VO 개념"
author: "김소연"
date: 2022-05-17 21:00:00 -0500
categories: [Web Application]
tags: [Java]
---



## DAO (Data Access Object)

- DB의 Data에 접근하기 위한 객체이다.
  - DB에 접근하여 **데이터를 삽입, 삭제, 조회 등 조작할 수 있는 기능을 수행**한다.
  - MVC패턴의 'Model'에서 이와 같은 일을 수행한다.
- DB 접근을 하기 위한 로직과 비즈니스 로직을 분리하기 위해 사용한다.
- 사용자는 자신이 필요한 Interface를 DAO에게 던지고, DAO는 이 Interface를 구현한 객체를 사용자에게 편리하게 사용할 수 있도록 반환한다.
- 데이터베이스와 연결할 Connection까지 설정되어 있는 경우가 많다.
  - MyBatis 등을 사용할 경우 커넥션풀까지 제공되고 있기 때문에 DAO를 별도로 만드는 경우는 드물다.



## DTO (Data Transfer Object)

- 계층 간 데이터 교환을 위한 자바 빈즈(Java Beans)이다.
  - Java Beans : Java로 작성된 소프트웨어 컴포넌트를 지칭하는 단어
- **로직을 가지고있지 않는 데이터 객체**이고, getter, setter 메소드만 가지고 있다.
- DB에서 Data를 얻어 Service나 Controller 등으로 보낼 때 사용하는 객체이다.

```java
@Getter @Setter
public class MemberDto {

    @NotBlank(message = "이름은 필수 입력 값입니다.")
    private String name;

    @NotEmpty(message = "이메일은 필수 입력 값입니다.")
    @Email(message = "이메일 형식으로 입력해주세요.")
    private String email;

    @NotEmpty(message = "비밀번호는 필수 입력 값입니다.")
    @Length(min=8, max=16, message = "비밀번호는 8자 이상, 16자 이하로 입력해주세요.")
    private String password;

    @NotEmpty(message = "주소는 필수 입력값입니다.")
    private String address;

}
```



![dto](/assets/img/dto.png){:width="80%" height="80%"}



## VO (Value Object)

- VO는 값 오브젝트로써 값을 위해 쓰인다.
  - Read only 특징을 가진다. 
    - 자바에서 값 타입을 표현하기 위해 불변 클래스를 만들어 사용
    - 사용하는 도중에 변경 불가능하며 오직 읽기만 가능 (getter기능만 존재)

#### DTO와 VO의 차이점

- DTO는 가변적인 성격을 가지고 있지만, VO는 불변의 성격을 가지고 있다.
  - DTO는 인스턴스 개념이고, VO는 리터럴 개념



### 출처📎


- [https://m.blog.naver.com/cjhol2107](https://m.blog.naver.com/cjhol2107/221757079506)
- [https://rninche01.tistory.com](https://rninche01.tistory.com/entry/web-DAO-DTO-VO-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC)
- [https://gmlwjd9405.github.io](https://gmlwjd9405.github.io/2018/12/25/difference-dao-dto-entity.html)
