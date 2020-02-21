

# SpringBootTest

Test module

1. Spring-boot-test
2. spring-boot-test-autoconfigure

Spring Boot 에서는 spring-boot-starter-test dependency를 추가

@SpringBootTest 라는 annotation을 제공

- MockTest

  Contoller

  ```java
  package com.minsoonss.springboot;
  
  import org.springframework.web.bind.annotation.GetMapping;
  import org.springframework.web.bind.annotation.RestController;
  
  /**
   * @author SoonMin Kwon. On 2020.02.21 10:25
   */
  @RestController
  public class SimpleController {
  
      @GetMapping("/")
      public String index() {
          return "Hello";
      }
  }
  ```

  TestCode

  ```java
  package com.minsoonss.springboot;
  
  import org.junit.Test;
  import org.junit.runner.RunWith;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
  import org.springframework.boot.test.context.SpringBootTest;
  import org.springframework.test.context.junit4.SpringRunner;
  import org.springframework.test.web.servlet.MockMvc;
  
  import static org.assertj.core.api.Assertions.assertThat;
  import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
  import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print;
  import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
  import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
  
  /**
   * @author SoonMin Kwon. On 2020.02.21 10:27
   */
  @SpringBootTest()
  @AutoConfigureMockMvc
  @RunWith(SpringRunner.class)
  public class SimpleControllerTest {
  
      @Autowired
      MockMvc mockMvc;
  
      @Test
      public void index() throws Exception{
          assertThat(mockMvc).isNotNull();
          mockMvc.perform(get("/"))
                  .andExpect(status().isOk())
                  .andExpect(content().string("Hello"))
                  .andDo(print());
      }
  }
  ```



 ### Test Configuration

1. 원하는 Bean을 nested class형태로 만들기 - **nested class 는 static으로 만들어야함**

   ```java
   /**
    * @author SoonMin Kwon. On 2020.02.21 10:27
    */
   @SpringBootTest
   @AutoConfigureMockMvc
   @RunWith(SpringRunner.class)
   public class SimpleControllerTest {
   
       @TestConfiguration
       static class TestConfig {
   
           @Bean
           public String myBean() {
               return "myBean";
           }
       }
   
       @Autowired
       MockMvc mockMvc;
   
       @Autowired
       String myBean;
   
       @Test
       public void index() throws Exception{
         	assertThat(myBean).isNotNull();
           assertThat(mockMvc).isNotNull();
           mockMvc.perform(get("/"))
                   .andExpect(status().isOk())
                   .andExpect(content().string("Hello"))
                   .andDo(print());
       }
   }
   ```

2. Test Configuration 제외시키기 - 따로 클래스로 빼게되면 해당 빈을 가져올 수 없다. -> 테스트클래스에서 @Import를 해야한다.

   ```java
   package com.minsoonss.springboot;
   
   import org.springframework.context.annotation.Bean;
   import org.springframework.boot.test.context.TestConfiguration;
   
   /**
    * @author SoonMin Kwon. On 2020.02.21 14:08
    */
   @TestConfiguration
   public class TestConfig {
       
       @Bean
       public String myBean() {
           return "myBean";
       }
   }
   
   ```

3. 실행하는 서버와 테스트하기

   `@SpringBootTest(webEnvironment=WebEnvironment.MOCK)` 를 사용하면 가짜 서블릿컨테이너가 뜨게됨

   `@SpringBootTest(webEnvironment=WebEnvironment.RANDOM_PORT)`  를 사용하면 진짜 서블릿컨테이너가 뜨게된다.

   `@WebMvcTest` 와 `@WebFluxTest` 는 단 하나의 컨트롤러를 테스트하기 위한 annotation

   ```java
   @SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
   @AutoConfigureMockMvc
   @RunWith(SpringRunner.class)
   public class SimpleControllerTest {
       
       @Autowired
       TestRestTemplate testRestTemplate;
       
       @Test
       public void testWithRestTemplate() throws Exception{
           String body = this.testRestTemplate.getForObject("/", String.class);
           System.out.println(body);
           assertThat(body).isEqualTo("Hello");
       }
   }
   ```

4. @MockBean

   어떠한 빈에 영향을 주지않고 테스트 빈을 설정할 수 있다

   아래와 같이 SimpleController는 SimpleService를 가져다 사용하고 SimpleService의 getName()은 "Remote Service" 를 리턴하지만 Test에서 MockBean을 만들어 "Mock"을 리턴하도록 적용

   **DB data를 변환하지 않고 사용할때 주로 사용**

   ```java
   /**
    * @author SoonMin Kwon. On 2020.02.21 10:25
    */
   @RestController
   public class SimpleController {
   
       @Autowired
       SimpleService simpleService;
   
       @GetMapping("/")
       public String index() {
           return simpleService.getName();
       }
   }
   ```

   ```java
   /**
    * @author SoonMin Kwon. On 2020.02.21 14:37
    */
   @Service
   public class SimpleService {
   
       public String getName() {
           return "Remote Service";
       }
   }
   ```

   ```java
   /**
    * @author SoonMin Kwon. On 2020.02.21 10:27
    */
   @SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
   @AutoConfigureMockMvc
   @RunWith(SpringRunner.class)
   public class SimpleControllerTest {
   
       @Autowired
       TestRestTemplate testRestTemplate;
   
       @MockBean
       SimpleService simpleService;
   
       @Test
       public void testWithRestTemplate() throws Exception{
           given(simpleService.getName()).willReturn("Mock");
   
           String body = this.testRestTemplate.getForObject("/", String.class);
           System.out.println(body);
           assertThat(body).isEqualTo("Mock");
       }
   }
   ```

   