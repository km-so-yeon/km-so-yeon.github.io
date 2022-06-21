---
title: "[Swagger] 스웨거 설명, 사용법"
author: "김소연"
date: 2022-06-20 21:00:00 -0500
categories: [Web Application]
tags: [Swagger]
---



## Swagger란

- REST API 문서를 자동으로 생성해주는 오픈소스 프레임워크다.



#### 기능

- API 설계
- API 개발
- API 테스트
- API 모킹 및 시각화
- API 모니터링
- API 관리





## 설정

#### Spring boot에 Swagger 적용

1. 의존성 추가 (maven)

```xml
<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-swagger2</artifactId>
	<version>2.9.2</version>
</dependency>
<dependency>
   <groupId>io.springfox</groupId>
   <artifactId>springfox-swagger-ui</artifactId>
   <version>2.9.2</version>
</dependency>
```



2. `SwaggerConfig` 클래스 생성

```java
import java.util.HashSet;
import java.util.Set;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotation.EnableSwagger2;

@Configuration
@EnableSwagger2
public class SwaggerConfig {

  	private ApiInfo apiInfo() {
    	return new ApiInfoBuilder()
          		.title("swagger 제목")
          		.description("swagger 설명")
          		.version("1.0")
          		.build();
  	}
  
  	private Set<String> getConsumeContentTypes() {
    	Set<String> consumes = new HashSet<>();
      	consumes.add("application/json;carset=UTF-8");
      	consumes.add("application/x-www-form-urlencoded");
      	return consumes;
  	}
  
  	private Set<String> getProduceContentTypes() {
    	Set<String> produces = new HashSet<>();
      	produces.add("application/json;charset=UTF-8");
      	return produces;
  	}
  
  	@Bean
	public Docket api() {
        return new Docket(DocumentationType.SWAGGER_2)
                .select()
                .apis(RequestHandlerSelectors.any())
                .paths(PathSelectors.any())
                .build();
    }
}
```

- **.paths(PathSelectors.any())** : 해당 패키지 하위에 있는 모든 url에 적용
  -  `paths(PathSelectors.ant("/api/**")) ` 로 설정할 경우 RequestMapping에서 설정한 url `/api` 하위의 모든 API를 보도록 설정하는 것

- **getConsumeContentTypes(), getProduceContentTypes()** : 생략 가능

  ​



3. Controller에 Swagger 어노테이션 추가

- @Api : Swagger 최상단 Controller 명칭
- @ApiOperation : Swagger에 사용하는 API에 대한 간단 설명
- @ApiImplicitParam : Swagger에 사용하는 파라미터에 대해 설명
  - @ApiImplicitParams 로 @ApiImplicitParam 을 여러 개 포함할 수 있다.

예시1

```java
@RestController
@RequestMapping("/api")
@Api(tags = {"ToyProject API Test"})
public class UserController {
  
  	@ApiOperation(value = "사용자 정보등록" , notes = "상세한 사용자 정보에 대해서 출력한다.")
    @ApiImplicitParams({
            @ApiImplicitParam(name = "email", value = "사용자의 이메일을 입력한다", required = false, dataType = "SearchVO", paramType = "string", defaultValue = ""),
            @ApiImplicitParam(name = "id", value = "사용자의 id값", required = false, dataType = "SearchVO", paramType = "string", defaultValue = ""),
            @ApiImplicitParam(name = "page", value = "페이지 숫자", required = false, dataType = "SearchVO", paramType = "int", defaultValue = ""),
    })
    @RequestMapping(method = RequestMethod.POST, path = "/postRequest")
    public SearchVO postRequest(@RequestBody SearchVO searchVO){
        return searchVO;
    }
}
```

예시2

```java
@RequiredArgsConstructor
@RestController
@RequestMapping("/api/v1")
@Api(tags = {"ToyProject API Test"})  // Swagger 최상단 Controller 명칭
public class PostsApiController {

    private final PostsService postsService;

    @GetMapping("/posts/{id}")  // 조회 API
    @ApiOperation(value = "글 조회", notes = "글 조회 API")
    @ApiImplicitParam(name = "id", value = "글 아이디")  // Swagger에 사용하는 파라미터에 대해 설명
    public PostsResponseDto findById (@PathVariable Long id) {
        return postsService.findById(id);
    }
  
}
```



4. Dto에 Swagger 추가 (데이터를 받은 @RequestBody 형식에 파라미터 표현)

- @ApiModelProperty

```java
@Getter
public class PostsResponseDto {

    @ApiModelProperty(example = "글 아이디")  // Swagger에 해당 필드가 무엇인지 나타냄
    private Long id;

    @ApiModelProperty(example = "글 제목")
    private String title;

    @ApiModelProperty(example = "글 설명")
    private String description;
    
    // 생략
}
```



#### Spring Security와 Swagger

Spring Security를 사용한다면 기본적으로 Security에 예외 처리되지 않은 URL 매핑들은 필터를 거치지 않으면 통과시키지 않기 때문에 아래 내용을 추가해주어야 한다.

1. `antMatcher`에 `"/v2/api-docs", "/configuration/**", "/swagger*/**", "/webjars/**"` 추가

```java
@Override
    protected void configure(HttpSecurity http) throws Exception {
        http.cors().and().csrf().disable()
                .authorizeRequests()
                .antMatchers("/user/signup", "/user/login", "/exception/**","/item/**", "/v2/api-docs", "/configuration/**", "/swagger*/**", "/webjars/**")
                .permitAll()
                .anyRequest()
                .authenticated()
                .and()
                .exceptionHandling().authenticationEntryPoint(new AuthenticationExceptionHandler())
                .and()
                .sessionManagement()
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS);

        http.addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter.class);
    }
```

포스트맨에서 Swagger를 JSON 방식으로 확인할 수도 있고, Swagger UI로 생성된 API 문서를 html로 확인할 수도 있다.



## 사용법

1. `localhost:8080/swagger-ui.html` 으로 접속
2. `Try it out` 버튼 클릭 후 파라미터가 필요할 경우 작성 후 Execute





- [hsayho.log](https://velog.io/@seho100/Spring-boot%EC%97%90-Swagger-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0Maven)
- [haddoddo](https://haddoddo.tistory.com/entry/Spring-Boot-STS-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8%EC%97%90-swagger-%EC%97%B0%EB%8F%99-%EB%B0%8F-%EC%82%AC%EC%9A%A9%EB%B2%95)
- [gaga-kim](https://gaga-kim.tistory.com/entry/Spring-Boot-Swagger%EB%A5%BC-%ED%99%9C%EC%9A%A9%ED%95%9C-API-%EB%AC%B8%EC%84%9C-%EC%9E%90%EB%8F%99%ED%99%94)