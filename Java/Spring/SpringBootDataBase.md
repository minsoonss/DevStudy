# Spring Boot DataBase

Spring Framework에는 SQL database를 사용하는 다양한 방법이 존재

1. `JdbcTemplate`을 이용한 방법
2. `Object Relational Mapping(ORM)`을 사용하는 방법

Spring Data는 추가적인 기능을 제공 ->  `Repository` 구현해주는 기능 제공

### DataSource 설정 방법

1. `spring.datasource.*` 을 이용하여 설정
   - `spring.datasource.url`
   - `spring.datasource.username`
   - `spring.datasource.password`
2. @ConfigurationProperties("app.datasource") 를 설정한 Bean생성
   - 비추천
     - 자동완성이 안됨
     - hikari가 설정돼있는 경우 url property값이 적용이 안됨 -> jdbc-url 로 가능

### Embedded Database Support

in-memory embedded database를 사용하게 되면 테스트할 때 유용하다

다만 영속성이 없기 때문에 지워지게 되며 설정을 미리 해놔야함

Auto Configuration 지원

- H2, HSQL, Derby 지원 
- URL들을 제공할 필요 없음 -> dependency만 추가

Embedded database Auto Configuration을 하기 위해서는 `spring-jdbc`가 추가되어야한다

- `spring-boot-starter-data-jpa` 에서 같이 들어오게 됨

  - Dependecy

    ```xml
    <dependency>
    	<groupId>org.springframework.boot</groupId>
    	<artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
    	<groupId>org.hsqldb</groupId>
    	<artifactId>hsqldb</artifactId>
    	<scope>runtime</scope>
    </dependency>
    ```

  - SimpleCode

    ```java
    @Bean
    	public ApplicationRunner runner(final JdbcTemplate jdbcTemplate) {
    		return new ApplicationRunner() {
    			@Override
    			public void run(ApplicationArguments args) throws Exception {
    				Integer cnt =
    						jdbcTemplate.queryForObject("SELECT COUNT(*) FROM Customers", Integer.class);
    				System.out.println(cnt);
    			}
    		};
    	}
    ```

  - Query

    ```sql
    --schema
    DROP TABLE Customers IF EXISTS;
    
    CREATE table Customers (
        ID INTEGER IDENTITY PRIMARY KEY ,
        CustomerName varchar(255),
        Address varchar(255),
        City varchar(255),
        PostalCode varchar(255)
    );
    
    -- data
    Insert into Customers (CustomerName, Address, City, PostalCode) VALUES ('cardinal', 'skagen 21', 'stavanger', '4006');
    Insert into Customers (CustomerName, Address, City, PostalCode) VALUES ('minsoonss', 'sillim', 'seoul', '1234');
    
    ```


### Production Database

Datasource 선호

1. Spring boot 는 HikariCP를 선호 - 성능과 동시성(Concurrency)이 좋음
2. Tomcat DBCP
3.  위의 두개가 없으면 Commons DBCP2 사용

