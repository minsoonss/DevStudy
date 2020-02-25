# Bean - IOC Container

### Spring에서 `POJO(Plain Old Java Object)`를 Bean이라 한다.

- Spring `IOC`(Inversion Of Control) Container에 의해 관리된다.
- 자주 사용하는 객체를 `Singleton` 으로 만든다.
- Spring IOC Container에 의해 자바 객체가 만들어 지게 되면 해당 객체를 스프링 빈이라 한다.

## IOC(제어의 역전)

- DI (의존성 주입) 와 함께 설명된다.
  
  - DI는 객체 자체가 의존성을 가지는 것이 아닌 Framework에 의해 의존성이 주입된다.
  
- IOC는 DI를 통해 이루어진다.
  - 프로그램의 제어를 framework가 하는것이다.
  - 개발자가 설정을 하면 Container가 알아서 처리한다.
  
- 장점

  - 종속성 감소
  - 재사용성 증가
  - 테스트코드의 종류 증가
  - 코드의 가독성 증가

- DI 방법
  1. Constructor Injection
  
     ```java
     public class A {
       private final B b;
       
       private final C c;
       
       @Autowired
       public A(B b, C c) {
         this.b = b;
         this.c = c;
       }
     }
     ```
  
  2. Method Injection
  
     ```java
     public class A {
       private B b;
       
       private C c;
       
       public void setB(B b) {
         this.b = b;
       }
       
       public void setC(C c) {
         this.c = c;
       }
       
     }
     ```
  
  3. Field Injection
  
     ```java
     public class A {
       @Autowired
       private B b;
       @Autowired
       private C c;
     }
     ```
  
  **Setter Injection VS Constructor Injection**
  
  - Setter Injection
    - 선택적인 의존성 주입에 유리
    - 스프링 3.x에서 추천되던 방법
    - not-null 체크를 모두 구현해야하는 불편함
  - Constructor Injection
    - 필수적인 의존성 주입에 유리
    - 불변성 보장(개발자가 임의로 null이나 다른객체로 바꾸는것이 불가)
    - 순환참조 체크가능
    - 스프링 프레임워크에 종속적이지 않음(스프링 4.3부터 완전히 분리 - Autowired 생략가능 - 생성자가 하나일때만 가능??)
    - 스프링 4.x부터 setter injection이 아닌 constructor injection 권장
  
  **Constructor Injection이 선호되는 이유**
  
  - final로 선언되기 때문에 Component를 변경이 불가능한 `immutable 상태`로 선언 가능
  -  `BeanCurrentlyInCreationException`이 발생해서 순환 의존성 파악 가능
  - `특정 DI Container에 의존하지 않으며` 쉽게 단위 테스트도 가능하고 필요하다면 다른 DI Container로 전환 가능
  - DI Container가 Component를 주입을 못한 경우 실제 Method가 호출되기 이전인 생성자에서 Handling 가능
  
  



