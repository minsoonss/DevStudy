# SpringBoot MVC Security

- 상세한 로그찍기

  `org.springframework.boot.autoconfigure.security` 를 `INFO` level 로 로그를 찍는다.
  
- Dependency

  ```xml
  <dependency>
  	<groupId>org.springframework.boot</groupId>
  	<artifactId>spring-boot-starter-security</artifactId>
  </dependency>
  ```

- 간단 예제

  ```java
  @RestController
  public class UserController {
  
      @GetMapping("/user")
      public User currentUser() {
          User user = new User();
          user.setName("minsoonss");
          user.setAge(29);
          user.setHeight(175);
          return user;
      }
  }
  ```

  ```java
  /**
   * @author SoonMin Kwon. On 2020.02.25 11:41
   */
  @Component
  public class AuthenticationEventHandlers {
  
      @EventListener
      public void handleBadCredential(AuthenticationFailureBadCredentialsEvent event) {
          System.out.println("### "+event.getAuthentication().getPrincipal() + "인증 시도중...");
          System.out.println("### 비번 틀림");
      }
  }
  ```

### MVC Security

- Security configuration은 `SecurityAutoConfiguration` 을 구현한다

- security를 등록하게되면 기본적으로 다 막힘

  - static 파일들도 막힘 ex)http://localhost:8080/hello.html

- Test -> html 허용해보기

  ```java
  /**
   * @author SoonMin Kwon. On 2020.02.25 13:58
   */
  @Configuration
  public class SecurityConfig extends WebSecurityConfigurerAdapter {
  
      @Override
      protected void configure(HttpSecurity http) throws Exception {
          http
            .authorizeRequests()
               .requestMatchers(PathRequest
                                .toStaticResources()
                                .atCommonLocations()).permitAll()
                  .requestMatchers(new AntPathRequestMatcher("/hello.html")).permitAll()
                  .anyRequest().fullyAuthenticated()
                  .and()
                  .formLogin().permitAll()
                  .and()
                  .logout().permitAll();
  -------------------------------위 아래 같음-------------------------------
        http.authorizeRequests()
                  .antMatchers("/hello.html").permitAll()
                  .anyRequest().fullyAuthenticated()
                  .and()
                  .formLogin().permitAll()
                  .and()
                  .logout().permitAll();
      }
  }
  ```

- @EnableGlobalMethodSecurity

  - parameters
    - securedEnable = true -> @Secured 사용가능

