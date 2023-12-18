---
title: "[JPA] JPA 구동원리"
author: "김소연"
date: 2023-12-18 19:00:00 -0500
categories: [Web Application, JPA]
tags: [JPA]
---





# JPA 구동원리

![jpa-m](/assets/img/jpa-m.png){:width="80%" height="80%"}

1. Persistence 클래스에서 META-INF/persistence.xml 에서 설정 정보를 읽는다. (Maven일 경우)
   - Gradle일 경우 application.yml 설정파일
2. EntityManagerFactory를 만든다.
   - EntityManagerFactory는 JPA를 동작시키기 위한 기반 객체이고, JPA 구현체에 따라 DB 커넥션 풀을 생성하기 때문에 EntityManagerFactory를 생성할 때마다 비용이 많이 들게 된다.
   - 따라서 EntityManagerFactory는 애플리케이션 전체에서 1번만 생성하고 공유해서 사용해야 한다.
3. EntityManager를 필요할 때마다 생성해서 JPA를 동작한다.
   - EntityManager는 JPA의 대부분 기능을 제공한다.
   - EntityManager를 사용해서 엔티티를 DB에 CRUD할 수 있다.
   - DB 커넥션을 유지하면서 DB와 통신할 정도로 밀접한 관계가 있으므로 EntityManager를 쓰레드 간에 공유 또는 재사용하면 안된다.

![jpa-m2](/assets/img/jpa-m2.png){:width="80%" height="80%"}



# Persistence Context

- Persistence Context(영속성 컨텍스트)는 Entity를 영구 저장하는 환경이다.
- EntityManagerFactory에서 생성된 EntityManager로 Entity를 관리할 때 영속성 컨텍스트에 엔티티를 보관하고 관리한다.



## Entity 생명주기

Entity는 비영속, 영속, 준영속, 삭제 상태가 있다.

![jpa-m3](/assets/img/jpa-m3.png){:width="80%" height="80%"}

- 비영속 (New)
  - 영속성 컨텍스트와 전혀 관계가 없는 상태
  - Entity 객체를 `persist()` 하지 않아 영속성 컨텍스트에 저장되지 않은 상태이다.
- 영속 (Managed)
  - Entity가 영속성 컨텍스트에 저장되어 관리될 때
  - 객체를 EntityManager의 `persist()` 로 등록할 때 혹은 DB에서 Entity 데이터를 불러왔을 때 영속 상태가 된다.
- 준영속 (Detached)
  - 영속 상태의 엔티티를 영속성 컨텍스트에서 제거했을 때
  - EntityManager의 `detach()` 를 사용하면 특정 엔티티가 준영속 상태가 된다.
- 삭제 (Removed)
  - Entity를 영속성 컨텍스트와 데이터베이스에서 제거한다.
  - 객체에 EntityManager의 `remove()` 를 사용했을 때 삭제 상태가 된다.



## 영속성 컨텍스트 특징 및 동작방식

### 1차 캐시

- 영속성 컨텍스트는 내부에 1차 캐시를 가지고 있다.

- 객체를 persist를 사용해서 영속 상태로 만들면 1차 캐시에 저장된다.
- 캐시는 Map으로 되어있고, 식별자를 key로 하고, Entity는 value가 된다.
- 식별자는 DB 기본키와 매핑된다.



### Entity 조회

![jpa-m4](/assets/img/jpa-m4-find.png){:width="80%" height="80%"}

- Entity를 find하면 JPA는 기본적으로 1차 캐시를 먼저 찾는다.
- 만약 1차 캐시에 Entity가 없다면 DB에서 조회한다.
- 이후 DB에서 조회한 데이터를 1차 캐시에 저장하고, Entity를 반환한다. 여기서의 Entity는 영속 상태이다.



#### 영속된 Entity의 동일성 보장

```java
Member m1 = em.find(Member.class, 1);
Member m2 = em.find(Member.class, 1);
System.out.println(m1 == m2);
```

- 영속성 컨텍스트의 Entity에서 가져오기 때문에 m1과 m2는 같은 인스턴스이다.
- 즉, 영속성 컨텍스트는 성능상 이점과 Entity의 동일성을 보장한다.



### Entity 등록

![jpa-m5](/assets/img/jpa-m5-insert.png){:width="80%" height="80%"}

#### 트랜잭션을 지원하는 쓰기 지연 제공

- JPA에서는 트랜잭션을 commit하기 직전까지 Entity를 DB에 저장하지 않고, 내부 쿼리 저장소에 INSERT 구문을 모아둔다. 이를 트랜잭션을 지원하는 쓰기 지연이라고 한다.
- commit을 하게 되면 `flush()`를 실행해서 DB로 INSERT 구문을 보내게 된다.



### Entity 수정

![jpa-m6](/assets/img/jpa-m6-update.png){:width="80%" height="80%"}

#### 변경 감지(Dirty Checking)

- update문을 작성하는 SQL과 달리 JPA에서는 변경감지라는 기능을 이용해서 update가 진행된다.
- Entity가 영속성 컨텍스트에 보관될 때, 최초 상태를 복사해서 저장하게 되는데, 이를 스냅샷이라고 한다.
- 변경 감지는 `flush()`가 실행될 때 스냅샷과 현재 Entity를 비교하여 변경된 Entity를 찾고 수정 쿼리를 생성해서 SQL 저장소에 보내고, 이것을 DB로 보낸다.
- 변경 감지는 영속 상태의 Entity에만 적용된다.



### Entity 삭제

- `remove()` 메소드를 이용해서 Entity를 삭제한다.
- 영속성 컨텍스트에서 제거된 후 바로 DB에서 제거되지 않고 SQL 저장소에 삭제 쿼리가 저장되었다가 flush 호출 때 DB로 전달한다.



### Flush

flush를 실행하면

- 쓰기 지연 SQL 저장소의 쿼리를 DB로 전송
- 변경 감지가 동작해서 영속성 컨텍스트의 모든 Entity를 스냅샷과 비교해서 수정된 Entity 탐색 후 수정 쿼리를 만들어서 SQL 저장소에 저장



#### flush를 사용하는 방법

- `flush()`를 직접 호출
- 트랜잭션 commit 시 자동 호출
- JPQL 쿼리 실행 시 자동 호출
  - `find()` 메소드에는 flush가 실행되지 않는다.



### Entity 준영속

준영속 상태로 만드는 방법

- `detach(Entity)` : 특정 Entity를 준영속 상태로 전환
  - 1차 캐시부터 쓰기 지연 SQL 저장소까지 해당 Entity와 관련된 정보가 모두 제거도니다.
- `clear()` : 영속성 컨텍스트를 초기화
  - 영속성 컨텍스트 안에 있는 모든 Entity를 준영속 상태로 만든다. 즉, 모든 Entity가 `detach()` 된다고 생각하면 된다.
- `close()` : 영속성 컨텍스트 종료
  - 영속성 컨텍스트를 종료해서 관리하던 Entity들을 준영속 상태로 만든다.



### Entity Merge

![jpa-m7](/assets/img/jpa-m7-merge.png){:width="80%" height="80%"}

```java
Member mergedMember = entityManager.merge(member);
```

- `merge(Entity)`는 준영속 상태의 Entity를 다시 영속 상태로 만들 수 있다.
- member가 영속 상태가 되는 것이 아니라 영속 상태의 새로운 Entity가 반환되는 것이다.
- 비영속 상태의 Entity도 `merge(Entity)` 로 넘겨준 파라미터의 식별자로 영속성 컨텍스트를 조회하고 Entity가 없다면 DB를 조회한다. 만약 DB에서도 찾지 못하면 새로운 Entity를 생성해서 병합한다.
- 따라서 `merge(Entity)`는 save 혹은 update 기능을 수행할 수 있다.
- merge는 모든 필드를 교체하면서 null로도 변경될 가능상이 있기 때문에 잘 확인하고 사용해야 한다.




# Raw JPA

- 직접 EntityManager를 사용해서 Entity를 CRUD할 수 있다.
  - Hibernate에서 JPA의 핵심인 EntityManagerFactory, EntityManager, EntityTransaction을 상속받아서 구현한다.
  - Hibernate 외에도 DataNucleus, EclipseLink 등 다른 JPA 구현체를 사용해도 된다. 다만 그렇게 하지 않는 이유는 단지 Hibernate가 굉장히 성숙한 라이브러리이기 때문이다.



## 구현 방법

- JPA 라이브러리를 받고
- `javax.persistence`의 `@Entity` 어노테이션을 사용해서 Entity를 명시해주고
- Repository에서는 `EntityManager`의 의존성을 주입받아서 Entity를 CRUD한다.



### Repository 구현

```java
@Repository
public class MemberRepository {
	private final EntityManager em;
    
    // EntityManager 의존성 주입
    @Autowired
    public MemberRepository(EntityManager em) {
        this.em = em;
    }
    
    // 저장하기
    public Member save(Member member) {
        em.persist(member);
        return member;
    }
    
    // 키 값으로 조회하기
    public Member findById(Long id) {
        return em.find(SampleEntity.class, id);
    }
    
    // JPQL 쿼리 실행하기 (리스트)
    public List<Member> find() {
        return em.createQuery(
        "select m from member as m", Member.class)
            .getResultList();
    }
    
    // JPQL 쿼리 실행하기 (조건 설정 + 단건)
    public Optional<Member> findByName(String name) {
        return em.createQuery(
        "select m from member as m where m.name = :name", Member.class)
            .setParameter("name", name)
            .getResultList()
            .stream().findAny();
    }
```

- 조회
  - `find()`를 사용해서 식별자를 통해 조회할 수 있다.
  - JPQL을 사용해서 원하는 데이터를 조회할 수도 있다.
    - 단 건 조회 시 `getSingleResult()`를 사용할 경우 결과에 따라 error를 발생시킨다.
      - NoResultException: 결과가 없을 경우
      - NonUniqueResultException : 결과가 2개 이상일 경우
      - 따라서 getResultList를 사용해서 결과가 없을 경우 `findAny()`를 사용해서 Optional을 반환하고, Service에서 error를 처리하도록 한다.


- 저장
  - **트랜잭션 처리**를 하고 `persist()`**를 사용**해서 영속성 컨텍스트에 저장하고 트랜잭션 처리를 해서 commit이 되면 DB에 INSERT된다.
- 수정
  - JPA에서는 변경 감지 기능이 있기 때문에 **트랜잭션 처리**를 하고 **Entity를 수정**하면 자동으로 수정 쿼리가 생성되고 SQL 저장소에 보내져서 commit이 되면 DB에 반영된다.
- 삭제
  - **트랜잭션 처리**를 하고 `remove()` **를 사용**해서 영속성 컨텍스트에서 삭제하면 삭제 쿼리가 생성되어서 commit이 되면 DB에 반영된다.



# Spring Data JPA

- Spring Data JPA는 JPA를 한 단계 추상화시킨 `Repository`라는 인터페이스를 제공한다.
- `EntityManager`를 직접 사용할 필요없이 Entity를 CRUD할 수 있다.
  - `Repository` 인터페이스의 기본 구현체인 `SimpleJpaRepository` 코드를 보면 내부적으로 `EntityManager`를 사용하고 있다.
  - 그렇기 때문에 Spring Data JPA는 `Repository` 인터페이스에 정해진 규칙대로 메소드를 입력하면 Spring이 알아서 해당 메소드 이름에 적합한 쿼리를 날리는 구현체를 만들어서 Bean으로 등록해준다.





# 출처

- https://hangjastar.tistory.com/240
- https://ssdragon.tistory.com/53
- https://jiwondev.tistory.com/225
- https://suhwan.dev/2019/02/24/jpa-vs-hibernate-vs-spring-data-jpa/