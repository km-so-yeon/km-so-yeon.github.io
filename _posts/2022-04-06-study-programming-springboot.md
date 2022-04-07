---
title: "[SpringBoot] 로그인 기능 구현하기(스프링 시큐리티)"
author: "김소연"
date: 2022-04-06 22:00:00 -0500
categories: [Study, Programming]
tags: [SpringBoot]
---



### 로그인/로그아웃 구현

- 데이터베이스에서 회원 정보를 가져오는 역할 : UserDetailsService

  - loadUserByUsername() : 회원 정보를 조회하여 사용자의 정보와 권한을 갖는 UserDetails 인터페이스를 반환

- 회원 정보를 담기 위해서 사용하는 인터페이스 : UserDetails

  - 이 인터페이스를 직접 구현하거나 스프링 시큐리티에서 제공하는 User 클래스 사용
  - User 클래스는 UserDetails 인터페이스를 구현하고 있는 클래스

- 로그인/로그아웃 구현

  - UserDetailsService 구현 : com.shop.service.MemberService.java

    ```java
        @Override
        public UserDetails loadUserByUsername(String email) throws UsernameNotFoundException{
            Member member = memberRepository.findByEmail(email);
    
            if(member == null){
                throw new UsernameNotFoundException(email);
            }
    
            return User.builder()
                    .username(member.getEmail())
                    .password(member.getPassword())
                    .roles(member.getRole().toString())
                    .build();
        }
    ```

    - UserDetailsService 인터페이스의 loadUserByUsername() 메소드 오버라이딩
    - return User.builder() ... : UserDetail을 구현하고 있는 User 객체를 반환

  - com.shop.config.SecurityConfig.java

    ```java
    @Configuration
    @EnableWebSecurity
    public class SecurityConfig extends WebSecurityConfigurerAdapter {
    
        @Autowired
        MemberService memberService;
    
        @Override
        protected void configure(HttpSecurity http) throws Exception{
            http.formLogin()
                    .loginPage("/members/login")
                    .defaultSuccessUrl("/")
                    .usernameParameter("email")
                    .failureUrl("/members/login/error")
                    .and()
                    .logout()
                    .logoutRequestMatcher(new AntPathRequestMatcher("/members/logout"))
                    .logoutSuccessUrl("/");
        }
    
        ...
    
        @Override
        protected void configure(AuthenticationManagerBuilder auth) throws Exception{
            auth.userDetailsService(memberService)
                    .passwordEncoder(passwordEncoder());
        }
    }
    ```

    - .loginPage("/members/login") : 로그인 페이지 URL 설정
    - .defaultSuccessUrl("/") : 로그인 성공 시 이동할 URL 설정
    - .usernameParameter("email") : 로그인 시 사용할 파라미터 이름으로 email 지정
    - .failureUrl("/members/login/error") : 로그인 실패 시 이동할 URL 설정
    - .logoutRequestMatcher(new AntPathRequestMatcher("/members/logout")) : 로그아웃 URL 설정
    - .logoutSuccessUrl("/"); : 로그아웃 성공 시 이동할 URL
    - protected void configure( ... : 
      스프링 시큐리티에서 인증은 AuthenticationManager를 통해 이루어지며,
      AuthenticationManagerBuilder가 AuthenticationManager를 생성한다.
      userDetailService를 구현하고 있는 객체로 memberService를 지정
      비밀번호 암호화를 위해 passwordEncoder를 지정

- 로그인 페이지 생성 : resources/templates/member/memberLoginForm.html

- Spring security 테스트 코드 추가

  - spring-security-test 의존성 추가 : pom.xml

    ```xml
    		<dependency>
    			<groupId>org.springframework.security</groupId>
    			<artifactId>spring-security-test</artifactId>
    			<scope>text</scope>
    			<version>${spring-security.version}</version>
    		</dependency>
    ```

  - test 컨트롤러 생성 : test.com.shop.controller.MemberControllerTest.java

    ```java
    @SpringBootTest
    @AutoConfigureMockMvc
    @Transactional
    @TestPropertySource(locations="classpath:application-test.properties")
    class MemberControllerTest {
    
        @Autowired
        private MemberService memberService;
    
        @Autowired
        private MockMvc mockMvc;
    
        @Autowired
        PasswordEncoder passwordEncoder;
        
        public Member createMember(String email, String password){
        	... 생략
        }
        
        @Test
        @DisplayName("로그인 성공 테스트")
        public void loginSuccessTest() throws Exception{
            String email = "test@email.com";
            String password = "1234";
            this.createMember(email, password);
            mockMvc.perform(formLogin().userParameter("email")
                    .loginProcessingUrl("/members/login")
                    .user(email).password(password))
                    .andExpect(SecurityMockMvcResultMatchers.authenticated());
        }
        
        ... 생략
    }
    ```

    - private MockMvc mockMvc; : 
      MockMvc 클래스를 이용해 실제 객체와 비슷하지만 테스트에 필요한 기능만 가지는 가짜 객체
      객체를 이용하면 웹 브라우저에서 요청을 하는 것처럼 테스트할 수 있다.
    - .loginProcessingUrl("/members/login") : 회원 가입 메소드 실행 후 가입된 회원 정보로 로그인이 되는지 테스트
    - .andExpect(SecurityMockMvcResultMatchers.authenticated()); : 로그인 성공하여 인증되면 테스트 코드 통과

#### 로그인/로그아웃 화면 연동

로그인 상태일 경우 '로그아웃' 표시

- thymeleaf-extras-springsecurity5 의존성 추가 : pom.xml

  ```xml
  		<dependency>
  			<groupId>org.thymeleaf.extras</groupId>
  			<artifactId>thymeleaf-extras-springsecurity5</artifactId>
  		</dependency>
  ```

- 화면 수정 : header.html

  ```html
  <!DOCTYPE html>
  <html xmlns:th="http://www.thymeleaf.org"
        xmlns:sec="http://www.thymeleaf.org/extras/spring-security">
  
  <div th:fragment="header">
      <nav class="navbar navbar-expand-sm bg-primary navbar-dark">
          <button class="navbar-toggler" type="button" data-toggle="collapse"
                  data-target="#navbarTogglerDemo03" aria-controls="navbarTogglerDemo03"
                  aria-expanded="false" aria-label="Toggle navigation">
              <span class="navbar-toggler-icon"></span>
          </button>
          <a class="navbar-brand" href="/">Shop</a>
  
          <div class="collapse navbar-collapse" id="navbarTogglerDemo03">
              <ul class="navbar-nav mr-auto mt-2 mt-lg-0">
                  <li class="nav-item" sec:authorize="hasAnyAuthority('ROLE_ADMIN')">
                      <a class="nav-link" href="/admin/item/new">상품 등록</a>
                  </li>
                  
  				... 생략
                  
                  </li>
                  <li class="nav-item" sec:authorize="isAnonymous()">
                      <a class="nav-link" href="/members/login">로그인</a>
                  </li>
                  <li class="nav-item" sec:authorize="isAuthenticated()">
                      <a class="nav-link" href="/members/logout">로그아웃</a>
                  </li>
  ... 생략
  ```

  - xmlns:sec="http://www.thymeleaf.org/extras/spring-security"> : Spring Security 태그를 사용하기 위해 추가
  - sec:authorize="hasAnyAuthority('ROLE_ADMIN')" : 관리자 계정(ROLE_ADMIN)으로 로그인한 경우 상품 등록, 상품 관리 메뉴를 보여준다.
  - sec:authorize="isAnonymous()" : 로그인하지 않은 상태이면 로그인 메뉴를 보여준다.
  - sec:authorize="isAuthenticated()" : 로그인한 상태이면 로그아웃 메뉴를 보여준다.

### 페이지 권한 설정

- 상품 등록 페이지 생성(ADMIN만 접근 가능) : resouces/templates/item/itemForm.html

- 상품 등록 페이지에 접근하는 클래스 생성 : com.shop.controller.ItemController.java

- 인증되지 않은 사용자가 리소스를 요청할 경우 "Unauthorized" 에러를 발생하는 인터페이스 구현 : com.shop.config.CustomAuthenticationEntryPoint.java

- 특정 경로에 접근할 때 인증을 요구하도록 설정 : com.shop.config.SecurityConfig.java

  ```java
      @Override
      protected void configure(HttpSecurity http) throws Exception{
          http.formLogin()
                  .loginPage("/members/login")
                  .defaultSuccessUrl("/")
                  .usernameParameter("email")
                  .failureUrl("/members/login/error")
                  .and()
                  .logout()
                  .logoutRequestMatcher(new AntPathRequestMatcher("/members/logout"))
                  .logoutSuccessUrl("/");
  
          http.authorizeRequests()
                  .mvcMatchers("/", "/members/**", "/item/**", "/images/**").permitAll()
                  .mvcMatchers("/admin/**").hasRole("ADMIN")
                  .anyRequest().authenticated();
  
          http.exceptionHandling()
                  .authenticationEntryPoint(new CustomAuthenticationEntryPoint());
      }
      
      @Override
      public void configure(WebSecurity web) throws Exception {
          web.ignoring().antMatchers("/css/**", "/js/**", "/img/**");
      }
  ```

  - http.authorizeRequests() : 시큐리티 처리에 HttpServletRequest 이용한다는 의미
  - permitAll() : 모든 사용자가 인증(로그인)없이 접근할 수 있도록 설정
  - .mvcMatchers("/admin/**").hasRole("ADMIN") : /admin으로 시장하는 경로는 해당 계정이 ADMIN Role일 경우에만 접근 가능하도록 설정
  - .anyRequest().authenticated(); : 나머지 경로들은 모두 인증을 요구
  - http.exceptionHandling() ... : 인증되지 않은 사용자가 리소스에 접근하였을 때 수행되는 핸들러 등록
  - web.ignoring().antMatchers("/css/**", ... : static 디렉터리의 하위 파일들은 인증을 무시하도록 설정

- Member 엔티티 생성 시 ADMIN Role로 생성하도록 수정 : com.shop.entity.Member.java

  ```java
  member.setRole(Role.ADMIN);
  ```

- 현재 로그인된 사용자의 Role에 따라 상품 등록 페이지에 접근 가능한지 테스트 코드 작성 : test/java/com.shop.controller.ItemControllerTest.java

  ```java
  ... 생략
  
      @Test
      @DisplayName("상품 등록 페이지 권한 테스트")
      @WithMockUser(username = "admin", roles = "ADMIN")
      public void itemFormTest() throws Exception{
          mockMvc.perform(MockMvcRequestBuilders.get("/admin/item/new"))
                  .andDo(print())
                  .andExpect(status().isOk());
      }
  
      @Test
      @DisplayName("상품 등록 페이지 일반 회원 접근 테스트")
      @WithMockUser(username = "user", roles = "USER")
      public void itemFormNotAdminTest() throws Exception{
          mockMvc.perform(MockMvcRequestBuilders.get("/admin/item/new"))
                  .andDo(print())
                  .andExpect(status().isForbidden());
      }
  ```

  - @WithMockUser(username = "admin", roles = "ADMIN") : 현재 회원 이름이 admin, role이 ADMIN인 유저가 로그인 된 상태로 테스트를 할 수 있도록 해주는 어노테이션
  - MockMvcRequestBuilders.get("/admin/item/new") : 상품 등록 페이지에 get 요청
  - .andDo(print()) : 요청, 응답 메시지를 확인할 수 있도록 콘솔창에 출력
  - .andExpect(status().isOk()); : 응답 상태 코드가 정상인지 확인
  - .andExpect(status().isForbidden()); : 상품 등록 페이지 진입 요청 시 Forbidden 예외가 발생하면 테스트 통과





### 느낀점

인증, 인가에 대해서 개념은 알고 있었지만 스프링 시큐리티를 통해 개발하여 직접 경험해볼 수 있어서 좋았다.
스프링 시큐리티에 대한 원리를 더 자세히 공부하면 좋을 것 같다.



### 출처📎

백견불여일타 스프링 부터 쇼핑몰 프로젝트 with JPA