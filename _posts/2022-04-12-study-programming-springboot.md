---
title: "[JPA] 영속성 전이, 지연 로딩"
author: "김소연"
date: 2022-04-11 21:00:00 -0500
categories: [Study, Programming]
tags: [JPA]
---



## Spring Data Jpa - Auditing 기능

- 엔티티가 저장 또는 수정될 때 자동으로 등록일, 수정일, 등록자, 수정자를 입력
- 엔티티의 생성과 수정을 감시하고 있는 것
- 공통 멤버 변수들을 추상 클래스로 만들고, 해당 추상 클래스를 상속받는 형태로 엔티티 리팩토링



#### 구현

- 현재 로그인한 사용자의 정보를 등록자와 수정자로 지정하기 위해서 AuditorAware 인터페이스를 구현한 클래스 생성

  ```java
  public class AuditorAwareImpl implements AuditorAware<String> {
      @Override
      public Optional<String> getCurrentAuditor() {
          Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
          String userId = "";
          if(authentication != null){
              userId = authentication.getName();
          }
          return Optional.of(userId);
      }
  }
  ```

- Auditing 기능을 사용하기 위한 Config 파일 생성

  ```java
  @Configuration
  @EnableJpaAuditing
  public class AuditConfig {
  
      @Bean
      public AuditorAware<String> auditorProvider(){
          return new AuditorAwareImpl();
      }
  }
  ```

  - @EnableJpaAuditing : JPA의 Auditing 기능 활성화
  - auditorProvider() : 등록자와 수정자를 처리해주는 AuditorAware을 빈으로 등록

- 등록자, 수정자를 넣지 않는 테이블이 있을 경우 BaseTimeEntity만 상속 받을 수 있도록 클래스 생성

  ```java
  @EntityListeners(value = {AuditingEntityListener.class})
  @MappedSuperclass
  @Getter @Setter
  public abstract class BaseTimeEntity {
  
      @CreatedDate
      @Column(updatable = false)
      private LocalDateTime regTime;
  
      @LastModifiedDate
      private LocalDateTime updateTime;
  
  }
  ```

  - @EntityListeners(value = {AuditingEntityListener.class}) : Auditing을 적용하기 위해 @EntityListeners 어노테이션 추가
  - @MappedSuperclass : 공통 매핑 정보가 필요할 때 사용하는 어노테이션, 부모 클래스를 상속 받는 자식 클래스에 매핑 정보만 제공
  - @CreatedDate : 엔티티가 생성되어 저장될 때 시간을 자동으로 저장
  - @LastModefiedDate : 엔티티의 값을 변경할 때 시간을 자동으로 저장

- 등록일, 수정일, 등록자, 수정자를 모두 갖는 엔티티가 상속받을 엔티티

  ```java
  @EntityListeners(value = {AuditingEntityListener.class})
  @MappedSuperclass
  @Getter
  public abstract class BaseEntity extends BaseTimeEntity{
  
      @CreatedBy
      @Column(updatable = false)
      private String createdBy;
  
      @LastModifiedBy
      private String modifiedBy;
  
  }
  ```

- BaseEntity 클래스 상속

  ```java
  public class Member extends BaseEntity{ ...
  ```




### 출처📎

백견불여일타 스프링 부터 쇼핑몰 프로젝트 with JPA