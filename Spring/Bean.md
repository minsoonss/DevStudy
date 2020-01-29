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
- DI 방법
  1. Constructor Injection
  2. Method Injection
  3. Field Injection
- 장점
  - 종속성 감소
  - 재사용성 증가
  - 테스트코드의 종류 증가
  - 코드의 가독성 증가

