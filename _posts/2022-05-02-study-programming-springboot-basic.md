---
title: "[Spring] 스프링 입문"
author: "김소연"
date: 2022-05-02 21:00:00 -0500
categories: [Study, Programming]
tags: [Spring]
---



## 프로젝트 생성

#### 스프링 부트로 프로젝트 생성
https://start.spring.io/

#### `IntelliJ` 로 프로젝트 열기
open - 프로젝트폴더\build.gradle

#### 프로젝트 구조

- src - main : 
  java
  resources(java 파일을 제외한 파일들)

- src - test : 테스트 코드와 관련된 소스들 (테스트 중요)

- build.gradle

  ```
  repositories {
  	mavenCentral()
  }
  
  dependencies {
  	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
  	implementation 'org.springframework.boot:spring-boot-starter-web'
  	testImplementation 'org.springframework.boot:spring-boot-starter-test'
  }
  ```

  - repositories : 라이브러리들을 다운받는 곳
  - dependencies : 라이브러리

- .gitignore

  - 깃에 올릴 때 올라갈 파일 설정

#### 실행

src\main\java\hello\hellospring\helloSpringApplication.java 의 main() 함수 - Run



## 라이브러리 살펴보기

- External Libraries 폴더 아래에 있음

- 사용하고자 라이브러리가 필요한 라이브러리까지도 가져온다.

  - 확인 방법

  1. 왼쪽 아래 아이콘 클릭 - Gradle
  2. 오른쪽 탭 gradle 클릭

#### 스프링 부트 라이브러리

- spring-boot-starter-web
  - spring-boot-start-tomcat : 톰캣(웹서버)
  - spring-webmvc : 스프링 웹 MVC
- spring-boot-starter-thymeleaf : 타임리프 템플릿 엔진(View)
- spring-boot-start(공통) : 스프링 부트 + 스프링 코어 + 로깅
  - spring-boot
    - spring-core
  - spring-boot-start-logging
    - logback, slf4j

#### 테스트 라이브러리

- spring-boot-starter-test
  - junit : 테스트 프레임워크





### 출처📎

[[인프런] 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8/dashboard)