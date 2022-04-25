---
title: "[JPA] 영속성 전이, 지연 로딩"
author: "김소연"
date: 2022-04-11 21:00:00 -0500
categories: [Study, Programming]
tags: [JPA]
---



## 영속성 전이 (cascade)

- 엔티티의 상태를 변경할 때 해당 엔티티와 연관된 엔티티의 상태 변화를 전파시키는 옵션
- 부모는 One, 자식은 Many
- 단일 엔티티에 완전히 종속적이고 부모 엔티티와 자식 엔티티의 라이프 사이클이 유사할 때 활용할 것

#### CASCADE 종류

- PERSIST : 부모 엔티티가 영속화될 때 자식 엔티티도 영속화
- MERGE : 부모 엔티티가 병합될 때 자식 엔티티도 병합
- REMOVE : 부모 엔티티가 삭제될 때 자식 엔티티도 삭제
- REFRESH : 부모 엔티티가 refresh되면 연관된 자식 엔티티도 refresh
- DETACH : 부모 엔티티가 detach되면 연관된 자식 엔티티도 detach 상태로 변경
- ALL : 부모 엔티티가 영속성 상태 변화를 자식 엔티티에 모두 전이

#### 옵션 설정 방법

-  `cascade = CascadeType.ALL`

  ```java
  @OneToMany(mappedBy = "order", cascade = CascadeType.ALL)
  private List<OrderItem> orderItems = new ArrayList<>();
  ```




### 고아 객체 제거

- 고아 객체 : 부모 엔티티와 연관 관계가 끊어진 자식 엔티티이다.

- 영속성 전이 기능과 같이 사용하면 부모 엔티티를 통해 자식의 생명 주기를 관리할 수 있다.

- @OneToOne, @OneToMany 어노테이션에서 사용할 것

  - 고아 객체 제거 기능은 참조하는 곳이 하나일 때만 사용해야 한다. 
    다른 곳에서도 참조하고 있는 엔티티를 삭제하면 문제가 생길 수 있다.


#### 옵션 추가 방법

- `orphanRemoval = true`

  ```java
      @OneToMany(mappedBy = "order", cascade = CascadeType.ALL
              , orphanRemoval = true)
      private List<OrderItem> orderItems = new ArrayList<>();
  ```


#### Cascade REMOVE 옵션과 차이점

- Cascade REMOVE : 부모 엔티티 삭제 시 연관된 자식 엔티티도 함께 삭제
- orphanRemoval : 부모 엔티티에서 자식 엔티티를 삭제



## 지연 로딩

#### 즉시 로딩

- 엔티티를 조회할 때 연관된 엔티티를 함께 조회
- 일대일, 다대일로 매핑할 경우 기본전략

```java
    @Test
    @DisplayName("지연 로딩 테스트")
    public void lazyLoadingTest(){
        Order order = this.createOrder();
        Long orderItemId = order.getOrderItems().get(0).getId();
        em.flush();
        em.clear();
        OrderItem orderItem = orderItemRepository.findById(orderItemId)
                .orElseThrow(EntityNotFoundException::new);
        System.out.println("Order class : " + orderItem.getOrder().getClass());
    }
```

- orderItem.getOrder().getClass() : orderItem 엔티티에 있는 order 객체의 클래스를 출력
  - 즉시 로딩할 경우 매핑된 엔티티를 함께 가지고 온다.  (order_item, item, orders, member 테이블)
  - 작성하고 있는 비즈니스 로직에서 사용하지 않을 데이터도 들고 오는 것
  - 즉시 로딩은 실무에서 사용하기 힘들다.

#### 지연 로딩

- 옵션 사용 : `FetchType.LAZY`

  ```java
      @ManyToOne(fetch = FetchType.LAZY)
      @JoinColumn(name = "item_id")
      private Item item;
  ```



```java
	@Test
    @DisplayName("지연 로딩 테스트")
    public void lazyLoadingTest(){
        Order order = this.createOrder();
        Long orderItemId = order.getOrderItems().get(0).getId();
        em.flush();
        em.clear();
        OrderItem orderItem = orderItemRepository.findById(orderItemId)
                .orElseThrow(EntityNotFoundException::new);
        System.out.println("Order class : " + orderItem.getOrder().getClass());
        System.out.println("===========================");
        orderItem.getOrder().getOrderDate();
        System.out.println("===========================");
    }
```

- orderItem.getOrder().getClass() : orderItem 엔티티만 조회
  - Order 클래스 조회 결과 : HibernateProxy (프록시 객체)
  - 프록시 객체 : 실제로 사용되기 전까지 데이터 로딩을 하지 않고, 실제 사용 시점에 조회 쿼리문이 실행된다.




### 출처📎

백견불여일타 스프링 부터 쇼핑몰 프로젝트 with JPA