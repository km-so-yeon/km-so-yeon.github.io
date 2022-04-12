---
title: "[JPA] 연관 관계 매핑"
author: "김소연"
date: 2022-04-07 22:00:00 -0500
categories: [Study, Programming]
tags: [JPA]
---



### 연관 관계 매핑

- 연관관계 매핑 종류

  - 일대일(1:1) : @OneToOne
  - 일대다(1:N) : @OneToMany
  - 다대일(N:1) : @ManyToOne
  - 다대다(N:N) : @ManyToMany

- 방향성

  - 단방향
  - 양방향

- 일대일 단방향 매핑

  ```java
      @OneToOne
      @JoinColumn(name = "member_id")
      private Member member;
  ```

    - @OneToOne : 회원 엔티티와 일대일 매핑 (장바구니와 관련된 소스가 없음 - 단방향)
      - member_id를 외래키로 가진다.

- 다대일 단방향 매핑

  ```java
      @ManyToOne
      @JoinColumn(name = "cart_id")
      private Cart cart;
  ```

  - @ManyToOne ... cart : 하나의 장바구니에 여러 개의 상품을 다대일 매핑
    - @JoinColumn에 설정된 값이 외래키로 추가된다. (@JoinColumn 어노테이션을 사용하는 엔티티에 컬럼 추가)

- 다대일/일대다 양방향 매핑

  - 양방향 매핑 : 단방향 매핑 2개

    ```java
    public class Order {
        ...
          
    	@ManyToOne
        @JoinColumn(name = "member_id")
        private Member member;
    }
    ```
    - @ManyToOne : 한명의 회원이 여러 번 주문할 수 있으므로 다대일 단방향 매핑 (회원 -< 주문)
    
    ```java
    public class OrderItem  {
    
        ...
            
        @ManyToOne
      @JoinColumn(name = "item_id")
        private Item item;
    
        @ManyToOne
      @JoinColumn(name = "order_id")
        private Order order;
    
        ...
    }
    ```
    - @ManyToOne ... item : 하나의 상품은 여러 주문 상품으로 들어갈 수 있으므로 주문 상품 기준으로 다대일 단방향 매핑 (상품 - <주문 상품)
    - @ManyToOne order : 한 번의 주문에 여러 개의 상품을 주문할 수 있으므로 다대일 단방향 매핑 (주문 -< 주문 상품)

  - 양방향 매핑에서는 '연관 관계 주인'을 설정해야 한다.

    - 객체 참조는 둘인데 외래키는 하나이므로 누가 외래키를 관리할지 정해야 한다.

      - 연관 관계의 주인은 외래키가 있는 곳으로 결정
      - 연관 관계의 주인이 외래키를 관리(등록, 수정 삭제)
      - 주인이 아닌 쪽은 연관 관계 매핑 시 mappedBy 속성의 값으로 연관 관계의 주인을 설정
      - 주인이 아닌 쪽은 읽기만 가능

  - Order 엔티티에 OrderItem 연관 관계 매핑 추가

      ```java
    @OneToMany(mappedBy = "order")
      private List<OrderItem> orderItems = new ArrayList<>();
    ```

    @OneToMany : 주문 상품 엔티티와 일대다 매핑

      외래키(order_id)가 order_item 테이블에 있으므로 연관 관계의 주인은 OrderItem 엔티티

      Order 엔티티는 주인이 아니므로 'mappedBy' 속성으로 연관 관계의 주인을 설정한다.

      속성값 : 연관관계 주인의 필드

- 다대다 매핑

  ```java
  public class Item {
  	@ManyToMany
  	@JoinTable{
  		name = "member_item",
  		joinColumns = @JoinColumn(name = "member_id"),
  		inverseJoinColumns = @JoinColumn(name = "item_id")
  	}
  	private List<Member> member;
  }
  ```

  - 실무에서는 사용하지 않는 매핑 관계

  - 사용하지 않는 이유 

    - 연결 테이블에는 컬럼을 추가할 수 없기 때문
    - 엔티티를 조회할 때 엔티티 사이 중간 테이블이 있기 때문에 어떤 쿼리문이 실행될지 예측하기 어렵기 때문

  - 연결 테이블용 엔티티를 하나 생성한 후 일대다, 다대일 관계로 매핑할 것



### 출처📎

백견불여일타 스프링 부터 쇼핑몰 프로젝트 with JPA