---
title: "[JPA] Querydsl 개념, 사용법"
author: "김소연"
date: 2022-05-12 21:00:00 -0500
categories: [Web Application]
tags: [Querydsl]
---



## Querydsl 개념

- JPQL을 코드로 작성할 수 있도록 도와주는 빌더 API
  - JPQL(Java Persistence Query Language) : 테이블이 아닌 엔티티 객체를 대상으로 검색하는 객체지향 쿼리
- 동작원리
  - 컴파일 단계에서 프로젝트 내의 @Entity 어노테이션을 선언한 클래스를 탐색하고 JPAAnnotationProcessor를 통해 쿼리 타입(QClass)을 생성한다.
  - 쿼리 타입(QClass)들은 Querydsl을 사용하여 메서드 기반으로 쿼리를 작성할 때 프로젝트에서 만든 도메인 클래스(Entity)의 구조를 설명해주는 메타데이터 역할을 하며, 쿼리의 조건을 설정할 때 사용된다.


- 장점

  - 고정된 SQL문이 아닌 조건에 맞게 동적으로 쿼리를 생성할 수 있다.
  - 비슷한 쿼리를 재사용할 수 있으며 제약 조건 조립 및 가독성을 향상시킬 수 있다.
  - 문자열이 아닌 자바 소스코드로 작성하기 때문에 컴파일 시점에 오류를 발견할 수 있다.
  - IDE의 도움을 받아 자동 완성 기능을 이용할 수 있기 때문에 생산성이 향상될 수 있다.




## 스프링에서 Querydsl 사용하기

#### 개발 환경 구성

**MAVEN 프로젝트**

1. 라이브러리 설정

 `pom.xml` 에 의존성 추가

```xml
<!-- Querydsl -->
<dependency>
	<groupId>com.querydsl</groupId>
	<artifactId>querydsl-jpa</artifactId>
	<version>5.0.0</version>
</dependency>
<dependency>
	<groupId>com.querydsl</groupId>
	<artifactId>querydsl-apt</artifactId>
	<version>5.0.0</version>
</dependency>

<plugin>
	<groupId>com.mysema.maven</groupId>
	<artifactId>apt-maven-plugin</artifactId>
	<version>1.1.3</version>
	<executions>
		<execution>
			<goals>
            	<goal>process</goal>
			</goals>
			<configuration>
				<outputDirectory>target/generated-source/java</outputDirectory>
				<processor>com.querydsl.apt.jpa.JPAAnnotationProcessor</processor>
			</configuration>
		</execution>
	</executions>
</plugin>
```



2. 프로젝트 우클릭 👉 Maven 👉 Generate Sources and Update Folders 메뉴 클릭


- QClass 생성 경로 : <outputDirectory>` 경로 (target/generated-source/java)

![springmvc](/assets/img/querydsl.jpg){:width="80%" height="80%"}



**Gradle 프로젝트**

1. `build.gradle`에 의존성 추가

```
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-web'

    // Querydsl 추가 시작
    implementation 'com.querydsl:querydsl-jpa'
    
    annotationProcessor "com.querydsl:querydsl-apt:${dependencyManagement.importedProperties['querydsl.version']}:jpa"
}
```

- Spring Data JPA 가 추가되어 있어야 한다.
- querydsl-apt : QClass 생성을 위한 라이브러리 추가 (annotationProcessor을 사용)
  - annotationProcessor : Java 컴파일러 플러그인

`java.lang.NoClassDefFoundError` 가 발생할 경우 아래와 같이 추가

```
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-web'

    // Querydsl 추가 시작
    implementation 'com.querydsl:querydsl-jpa'

    annotationProcessor "com.querydsl:querydsl-apt:${dependencyManagement.importedProperties['querydsl.version']}:jpa"
    annotationProcessor "jakarta.persistence:jakarta.persistence-api"
    annotationProcessor "jakarta.annotation:jakarta.annotation-api"
}
```



2. 프로젝트 실행

- QClass 생성 경로 (IntelliJ)

  Preferences 👉 Build, Execution, Deployment 👉 Build Tools 👉 Gradle 👉 Build and run using 




#### Querydsl 사용

1. JPAQueryFactory 사용

```java
class ItemRepositoryTest {
	@PersistenceContext
	EntityManager em;		// (1)
	
	@Test
    @DisplayName("Querydsl 조회테스트1")
    public void queryDslTest(){
        this.createItemList();
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);	// (2)
        QItem qItem = QItem.item;	// (3)
        JPAQuery<Item> query = queryFactory.selectFrom(qItem)
                .where(qItem.itemSellStatus.eq(ItemSellStatus.SELL))
                .where(qItem.itemDetail.like("%" + "테스트 상품 상세 설명" + "%"))
                .orderBy(qItem.price.desc());

        List<Item> itemList = query.fetch();	// (4)

        for(Item item : itemList) {
            System.out.println(item.toString());
        }
    }
}
```

(1) 영속성 컨텍스트를 사용하기 위해 **EntityManager** 빈을 주입

(2) **JPAQueryFactory**를 이용하여 쿼리를 동적으로 생성 (파라미터로 EntityManager)

(3) **QClass 객체**를 이용하여 Querydsl를 통해 쿼리를 생성

(4) fetch를 이용하여 쿼리 결과를 리스트로 반환

- JPAQuery 데이터 반환 메소드

  | 메소드                            | 설명                                 |
  | ------------------------------ | ---------------------------------- |
  | List\<T> fetch()               | 조회 결과 리스트로 반환                      |
  | T fetchOne                     | 조회 대상 1건인 경우 제네릭으로 지정한 타입 반환       |
  | T fetchFirst()                 | 조회 대상 중 1건만 반환                     |
  | Long fetchCount()              | 조회 대상 개수 반환                        |
  | QueryResult\<T> fetchResults() | 조회한 리스트와 전체 개수를 포함한 QueryResult 반환 |



2. QuerydslRedicateExecutor 사용

```java
public interface ItemRepository extends JpaRepository<Item, Long>,
        QuerydslPredicateExecutor<Item>{
 	// ...       
}
```

Repository에서 `QuerydslRedicateExecutor` 를 상속받는다. (JpaRepository를 상속받고 있어야 한다.)

```java
class ItemRepositoryTest {

  	@Autowired
    ItemRepository itemRepository;
  
	@Test
    @DisplayName("Querydsl 조회테스트2")
    public void queryDslTest2(){

        this.createItemList2();

        BooleanBuilder booleanBuilder = new BooleanBuilder();	// (1)
        QItem item = QItem.item;	// (2)
        String itemDetail = "테스트 상품 상세 설명";
        int price = 10003;
        String itemSellStat = "SELL";

        booleanBuilder.and(item.itemDetail.like("%" + itemDetail + "%"));	// (3)
        booleanBuilder.and(item.price.gt(price));

        if(StringUtils.equals(itemSellStat, ItemSellStatus.SELL)){
            booleanBuilder.and(item.itemSellStatus.eq(ItemSellStatus.SELL));
        }

        Pageable pageable = PageRequest.of(0, 5);	// (4)
        Page<Item> itemPagingResult = itemRepository.findAll(booleanBuilder, pageable);	// (5)
        System.out.println("total elements : " + itemPagingResult.getTotalElements());

        List<Item> resultItemList = itemPagingResult.getContent();	// (6)
        for(Item resultItem : resultItemList){
            System.out.println(resultItem.toString());
        }
}
```

(1) **BooleanBuilder**는 쿼리에 들어갈 조건을 만들어주는 빌더

(2) **QClass 객체**를 이용하여 Querydsl를 통해 쿼리를 생성

(3) 조건을 동적으로 추가한다.

(4) 데이터를 페이징하여 조회할 수 있도록 **Pageable** 객체를 생성한다.

(5) 조건에 맞는 데이터를 **Page** 객체로 받아온다.

(6) 조회된 데이터를 받아온다.



**개념**

- Predicate : 이 조건이 맞다고 판단하는 근거를 함수로 제공하는 것

  - BooleanBuilder는 Predicate의 구현체이며, 메소드 체인 형식으로 사용할 수 있다.

- Repository에 Predicate를 파라미터로 전달하기 위해 QuerydslRedicateExecutor 인터페이스를 상속받는다.

- QuerydslRedicateExecutor 인터페이스 메소드

  | 메소드                                   | 설명                  |
  | ------------------------------------- | ------------------- |
  | long count(Predicate)                 | 조건에 맞는 데이터의 총 개수 반환 |
  | boolean exists(Predicate)             | 조건에 맞는 데이터 존재 여부 반환 |
  | Iterable findAll(Predicate)           | 조건에 맞는 모든 데이터 반환    |
  | Page\<T> findAll(Predicate, Pageable) | 조건에 맞는 페이지 데이터 반환   |
  | Iterable findAll(Predicate, Sort)     | 조건에 맞는 정렬된 데이터 반환   |
  | T findOne(Predicate)                  | 조건에 맞는 데이터 1개 반환    |

- 장점 : Querydsl을 사용하기 위해 EntityManager을 주입하여 JpaQueryFactory를 생성하고 기본 쿼리도 직접 작성해야하는 수고를 덜 수 있다.




### 출처📎


- 개념

  - [https://wildeveloperetrain.tistory.com](https://wildeveloperetrain.tistory.com/92)
- 사용법

  - [https://gaemi606.tistory.com](https://gaemi606.tistory.com/entry/Spring-Boot-Querydsl-%EC%B6%94%EA%B0%80-Gradle-7x)
  - [https://jaime-note.tistory.com](https://jaime-note.tistory.com/80)
  - 백견불여일타 스프링부트 쇼핑몰프로젝트
