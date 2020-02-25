# Java-Spring 용어 및 개념

Java 와 Spring에 관련된 용어 및 개념을 생각나는대로 정리하는 파일



- fat.jar
  
  - class 파일과 dependency class파일이 전부 패키징 된 jar
  
- nested jar
  - nested - 중첩된
  - jar 파일 안에 jar가 포함된
  
- war VS jar
  - war - web application archive
    - 서블릿/jsp 컨테이너에 배포 가능 웹 어플리케이션- jsp, html, javascript, class, xml 및 웹 애플리케이션을 이루는 기타 자원을 모은 패키지 파일 포맷
  - jar - java archive
    - 자바 클래스파일들과 그 클래스파일들이 사용하는 리소스파일 및 메타데이터를 모아 배포하기 위한 소프트웨어 패키지 파일 포맷
  
- Facade
  - 뜻? 건물의 정면
  - 코드의 인터페이스를 제공 - 클라이언트가 서브시스템들의 각 모듈을 호출하도록 만들기 보다 서브시스템의 모듈들을 한데 모은 FacadeService를 구현 - 클라이언트는 FacadeService의 메소드를 호출
  
- slf4j-logback : spring boot 기본?
  - slf4j는 Logging Facade 이다!
  - logback은 slf4j의 구현체중 하나
  
- version
  - snapshot 과 M 버전은 인터페이스도 변경될 수 있다
  - snapshot - daily build
  - M - milestone (기간에 따른 목표 개발?)
  - RC - release candidate(베타 버전)
  - GA - general availability - 정식 릴리즈
  
- spring-boot 의 dependency 버전은 건들지마라! - spring 권장사항

- default package(package가 없는 상태 - 클래스를 만들었을때 패키지 정의가 안됨)를 사용하지마라
  - jar안에 있는 모든 클래스까지 다 스캔하기때문 - 성능문제 발생
  - 도메인 네임과 연관된 방식으로 패키지를 구성 후 그 아래 코드 작성하기 ex)com.example.project
  
- Spring boot는 자바 기반의 설정파일을 선호한다

- 굳이 xml 설정을 써야겠다면 - @ImportResource 어노테이션을 사용(경로는 루트 기준으로 찾음)

- AutoConfiguration을 쓰고 싶다면? - @EnableAutoConfiguration OR @SpringBootApplication

- 특정 AutoConfiguration을 쓰고싶지 않다면? - @EnableAutoConfiguration(exclude = {className})
  
  - application.yml 에서 spring.autoconfiguration.exclude에서 가능
  
- @SpringBootApplication = @EnableAutoConfiguration + @Configuration + @ComponentScan

- Maven에서 dependency추가할때 optional을 준다면? - gradle에는 optional을 지원하지 않는다.
  
  - 1번 프로젝트가 2번 프로젝트를 dependency로 추가한다면 2번 프로젝트 안에 있는 dependency를 추가하게 되는데 optional로 되어있는 dependency는 추가되지 않는다.
  
- logging level

  - TRACE, DEBUG, INFO, WARN, ERROR, FATAL, OFF

- ApplicationContext - Spring Boot 가 생기기전 아주 중요한 개념 -> BeanFactory

  - SpringApplication class의 Javadoc을 보면 제일 첫줄에 나옴
  - SpringApplication이 실행이 되면 제일먼저 ApplicationContext instance 를 만든다

- Reactive Web?? Servlet Web??

  - Reactive System??
    - 응답이 잘되고, 탄력적이며 유연하고 메세지 기반으로 동작하는 시스템
    - 유연하고 느슨한 결합을 가지고있으며 **확장성** 있다 -> 개발이 쉬워지며, 변경사항 적용이 쉽다.
    - **비동기메세징에 의존**
  - Reactive Programming
    - 비동기 데이터 스트림으로 프로그래밍하는 방식

- Listener는 Bean으로 구현을 못하지만 Runner는 Bean으로 구현

  - Listener는 ApplicationContext가 만들어지기전에도 끼어들 수 있다

- @ConfigurationProperties("minsoonss") - properties 혹은 yml에서 minsoonss로 되어있는 값을 가져와서 매핑해준다.

  ```java
  @Bean
  @ConfigurationProperties("minsoonss")
  @Validated
  public MinsoonssProperties minsoonssProperties() {
      return new MinsoonssProperties();
  }
  ```

- properties 혹은 yml 에서 값을 가져올때 @Validated를 사용하여 validation 체크를 할 수 있다.

  - @NotNull
  - @Valid
  - @NotEmpty
  - @Min
  - @Max ... 등

- @ConfigurationProperties VS @Value

  <img src="image-20200210141516690.png" alt="image-20200210141516690" style="zoom:150%;" />

- Logging
  - SpringBoot 는 Commons Logging을 사용하지만 logging으로 logback을 사용할 수 있도록 라우팅을 한다
  - spring.output.ansi.enabled=ALWAYS 하면 로깅 종류별로 색을 다르게 찍어준다
  - 실행시 debug=true로 찍을경우 특정 클래스에대해 debug로 찍어주는것이지 모든로그에 대해 debug로 찍어주는것은 아니다
  - logging.file.path, logging.file.name 을 이용하여 로그를 파일에 남길 수 있다
    - 둘 다 같이 쓰는 경우는 좀 더 구체적인 경로로 저장이 된다.(경로를 명시하는것보단 이름을 명시하는방향)
  - file은 10MB마다 lotate 된다
    - logging.file.max-size 로 파일 최대 크기를 지정할 수 있다
  - logging.file.max-history 로 해당날짜만큼 지나게되면 로그를 삭제해준다
  - logging.level.PACKAGE-NAME=debug 로 로깅레벨을 지정한다.
    - ex)logging.level.org.springframework.web=debug
  - **tip -> 로컬 개발시 hibernate schema generation 확인을 위해 hibernate는 trace로 걸어두기도 한다.**
  - logback-spring.xml 과 같은 파일로 logging 설정 가능
    - 확장 기능들을 설정가능
    - profile 별로 logging 설정 가능 
      - <springProfile name="dev"> 
      - <springProfile name="!dev">