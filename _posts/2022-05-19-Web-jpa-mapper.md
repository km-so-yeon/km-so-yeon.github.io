---
title: "[Spring] JPA와 MyBatis 차이"
author: "김소연"
date: 2022-05-19 21:00:00 -0500
categories: [Web Application]
tags: [Spring]
---



ORM과 SQL Mapper는 개발자가 직접 JDBC Programming을 하지 않도록 기능을 제공해주는 **Persistence Framework**이다.

- 영속성(Persistence) : 데이터들이 프로그램이 종료되어도 사라지지 않고 어떤 곳에 저장되는 개념
- 자바에서는 데이터의 영속성을 위한 JDBC를 지원해주는데, 이 매핑 작업을 개발자가 일일히 수행해야하는 번거로움이 있다.



## JPA (Java Persistence API)

ORM(Object Relational Mapping) 기술

- 자바 ORM의 기술 표준

- 대표적인 오픈소스로 Hibernate

- 1차 캐싱, 쓰기지연, 변경감지, 지연로딩 제공

- CRUD 메소드를 기본으로 제공

- 페이징 처리 구현되어 있음

- Spring + JPA 구조

  >Controller.java -> Service.java -> **Repository.java**
  >
  >전달되는 parameter : Entity.java

### ORM (Object Relational Mapping)

- 객체 관계 매핑
- **Object와 DB테이블을 매핑**하여 데이터를 객체화하는 기술
  - RDB테이블을 객체지향적으로 사용하게 해주는 기술
  - SQL문이 아닌 RDB에 데이터 그 자체와 매핑하기 때문에 **SQL을 직접 작성할 필요가 없다.**
    - 해당 객체와의 매핑에 필요한 SQL문을 알아서 만들어준다.
    - 따라서 어떤 RDB를 사용하든지 상관없다.
  - 복잡한 쿼리의 경우 JPQL을 사용하거나 SQL Mapper을 혼용하여 사용 가능하다.



## MyBatis (Java Persistence Framework)

Object Mapping 기술

- 자바에서 SQL Mapper를 지원해주는 프레임워크

- SQL문을 이용해서 RDB에 접근, 데이터를 객체화 시켜준다.

- SQL을 직접 작성하여 쿼리 수행 결과를 객체와 매핑시켜준다.

- 데이터 캐싱 기능으로 성능 향상

- Spring + MyBatis 구조

  > Controller.java -> Service.java -> **Mapper.java** -> **SQL.xml**
  >
  > 전달되는 parameter : VO 또는 Map

### SQL Mapper

- **Object와 SQL의 필드를 매핑**하여 데이터를 객체화 하는 기술
  - 객체와 테이블 간의 관계를 매핑하는 것이 아니다.
  - 직접 작성한 SQL문으로 해당되는 ROW를 읽어온 후 결과값을 객체화시켜 사용가능하게 만들어준다.
    - RDB에 따라 SQL문법이 다르기 때문에 특정 RDB에 종속적이다.



## JPA vs MyBatis

| JPA                                      | MyBatis                                  |
| ---------------------------------------- | ---------------------------------------- |
| SQL을 직접 작성하지 않아도 된다.<br />> 복잡한 쿼리는 처리하기가 어렵다.<br />> JPQL이나 SQL Mapper를 혼용하여 사용할 수 있다. | SQL을 직접 작성해야 한다.<br />> 복잡한 쿼리를 처리하기가 수월하다.<br />> 객체와 쿼리문 모두 관리해야 한다. CRUD 메소드를 직접 구현해야 한다. |
| 어떤 RDB를 사용하든지 상관없다.                      | 특정 RDB에 종속적이다.                           |
| DB변경 시 수정할 부분이 적다.                       | DB변경 시 수정할 부분이 많다.                       |




### 출처📎


- [https://dreaming-soohyun.tistory.com](https://dreaming-soohyun.tistory.com/entry/JPA%EC%99%80-MyBatis%EC%9D%98-%EC%B0%A8%EC%9D%B4-ORM%EA%B3%BC-SQL-Mapper)
- [https://velog.io/@rladuswl](https://velog.io/@rladuswl/ORM%EC%9D%98-%EA%B0%9C%EB%85%90-JPA%EC%99%80-MyBatis-%EC%B0%A8%EC%9D%B4)
